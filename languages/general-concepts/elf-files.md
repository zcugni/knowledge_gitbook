# ELF Files

## Generalities

* An elf file can be :
  * A relocatable object file
  * An executable
  * A core file
  * A shared object
* An ELF file is composed of :
  * An header
  * An optional program header \(for executable and shared object files\)
  * An optional section header \(for linking\)

## Header

The header is described as a C struct :

```c
#define EI_NIDENT 16

typedef struct {
    unsigned char e_ident[EI_NIDENT];
    uint16_t      e_type;
    uint16_t      e_machine;
    uint32_t      e_version;
    ElfN_Addr     e_entry;
    ElfN_Off      e_phoff;
    ElfN_Off      e_shoff;
    uint32_t      e_flags;
    uint16_t      e_ehsize;
    uint16_t      e_phentsize;
    uint16_t      e_phnum;
    uint16_t      e_shentsize;
    uint16_t      e_shnum;
    uint16_t      e_shstrndx;
} ElfN_Ehdr;
```

* `e_ident` An array specifying how to interpret the file, its bytes are divided as follows :
  * Each byte can be access through it's "name", for example `e_ident[EI_MAGO]`
  * The value 0 is often use for non-acceptable values, so i won't detail them
  * Bytes 0-1-2-3 \(`EI_MAG0`-`EI_MAG1`-`EI_MAG2`-`EI_MAG3`\) : Must be `0x7fELF`
  * Byte 4 \(`EI_CLASS`\) : Identifies the architecture, can be:
    * 1 for `ELFCLASS32`
    * 2 for `ELFCLASS64`
  * Byte 5 \(`EI_DATA`\) : Specify the encoding, can be :
    * 1 for `ELFDATA2LSB` : 2's complement, little-endian
    * 2 for `ELFDATA2MSB` :2's complement, big-endian
  * Byte 6 \(`EI_VERSION`\) : ELF's specification version
  * Byte 7 \(`EI_OSABI`\) : Specify the targeted OS and Application Binary Interface \(ABI\), there's multiple so I won't list them
  * Byte 8 \(`EI_ABIVERSION`\) : Version of the specified ABI
  * Byte 9-14 \(`EI_PAD`\) : Reserved bytes set to 0
  * Byte 15 \(`EI_NIDENT`\) : The size of the array
* `e_type` The type of ELF file
  * 1 for `ET_REL` A relocatable file
  * 2 for `ET_EXEC` An executable file
  * 3 for `ET_DYN` A shared object
  * 4 for `ET_CORE` A core file
* `e_machine` Specifies the required architecture for an individual file, there's multiple so i won't list them
* `e_version` The version of the file
* `e_entry`The virtual address to which the system first transfers control, thus starting the process. 
  * 0 If no associated entry point
* `e_phoff`The program header table's offset in bytes, 0 if not present
* `e_shoff`The section header table's offset in bytes, 0 if not present
* `e_flags` __Holds processor-specific flags associated with the file
  * Flag names take the form `EF<machine_flag>`
  * Currently, no flags defined
* `e_ehsize` The ELF header's size in bytes
* `e_phentsize`The size in bytes of a program header table entry
  * All entries are the same size
* `e_phnum`The number of entries in the program header table
  * If the number of entries is &gt;= 0xffff, it holds `0xffff` and the real number is in the `sh_info` of the initial entry in the section header table
* `e_shentsize` The size in bytes of a section header table entry
  * All entries are the same size
* `e_shnum` The number of entries in the section header table
  * If the number of entries is &gt;= 0xffff, it holds `0xffff` and the real number is in the `sh_info` of the initial entry in the section header table
* `e_shstrndx`The section header table index of the entry that is associated with the section name string table
  * If the index of the section name string table section is &gt;= 0xff00, it holds `0xffff` and the real index of the section name string table section is held in the `sh_link`of the initial entry in section header table

### Program header table

* The program header table is an array of structures, each describing a segment \(or other information the system needs to prepare the program for execution\)
* An object file segment contains one or more sections
* There's 2 structures depending on the architecture :

```c
typedef struct {
   uint32_t   p_type;
   Elf32_Off  p_offset;
   Elf32_Addr p_vaddr;
   Elf32_Addr p_paddr;
   uint32_t   p_filesz;
   uint32_t   p_memsz;
   uint32_t   p_flags;
   uint32_t   p_align;
} Elf32_Phdr;

typedef struct {
   uint32_t   p_type;
   uint32_t   p_flags;
   Elf64_Off  p_offset;
   Elf64_Addr p_vaddr;
   Elf64_Addr p_paddr;
   uint64_t   p_filesz;
   uint64_t   p_memsz;
   uint64_t   p_align;
} Elf64_Phdr;
```

* The difference is the position of the `p_flags`
* `p_type` Categorizes the segment
  * `PT_NULL` Mark the segment as **inactive**,  this allows the existence of ignored entries
  * `PT_LOAD` A **loadable** segment, described by `p_filesz` & `p_memsz`
    * Loadable  segment entries appear in ascending order, sorted on `p_vaddr`
  * `PT_DYNAMIC` **Dynamic** linking information
  * `PT_INTERP`Location &  size of a pathname to invoke as an **interpreter**
    * Most not be present more than once in a file
    * If present, it must precede any loadable segment entry
  *  `PT_NOTE` The location of **notes** \(ElfN\_Nhdr\).
  * `PT_SHLIB` **Reserved** but unspecified semantics
  * `PT_PHDR` The location & size of the **program header table itself** in the file and in the memory image of the program
    * Most not be present more than once in a file and only if the program header table is part of the memory image of the program
    * If present, it must precede any loadable segment entry
  * `PT_GNU_STACK` GNU extension used by the Linux kernel to control the state of the stack via the flags set in `p_flags`
* `p_offset` The offset of the start of segments
* `p_vaddr` The virtual address of the start of segments
* `p_paddr` The segment's physical address \(when relevant\), otherwise 0
* `p_filesz`The number of bytes in the file image of the segment
  * `p_memsz`The number of bytes in the memory image of the segment
  * If p\_memsz is larger than p\_filesz, the extra bytes are 0
  * p\_filesz can't be bigger than p\_memsz 
* `p_flags` A bit mask of flags
  * `PF_X`   An executable segment
  * `PF_W`   A writable segment
  * `PF_R`   A readable segment
  * A text segment commonly has the flags PF\_X and PF\_R
  * A data segment commonly has PF\_W and PF\_R
* `p_align` The value to which the segments are aligned in memory and in the file
  * Loadable process segments must have congruent values for `p_vaddr` & `p_offset`, modulo the page size
  * 0 & 1 means no alignment is required
  * Otherwise : 
    * p\_align should be a positive, integral power of two
    * p\_vaddr should equal p\_offset, modulo p\_align

### Section header table

* Locates all the file's sections
* It's an array of structures, there's 2 types of them, for each architecture, but they don't differ

```c
typedef struct {
   uint32_t   sh_name;
   uint32_t   sh_type;
   uint32_t   sh_flags;
   Elf32_Addr sh_addr;
   Elf32_Off  sh_offset;
   uint32_t   sh_size;
   uint32_t   sh_link;
   uint32_t   sh_info;
   uint32_t   sh_addralign;
   uint32_t   sh_entsize;
} Elf32_Shdr;

typedef struct {
   uint32_t   sh_name;
   uint32_t   sh_type;
   uint64_t   sh_flags;
   Elf64_Addr sh_addr;
   Elf64_Off  sh_offset;
   uint64_t   sh_size;
   uint32_t   sh_link;
   uint32_t   sh_info;
   uint64_t   sh_addralign;
   uint64_t   sh_entsize;
} Elf64_Shdr;
```

* `sh_name`The name of the section, it can be used as an index for the string table
* `sh_type` Categorizes the section's contents
  * `SHT_NULL` Marks the section header as **inactive**
    * It does not have an associated section
  * `SHT_PROGBITS` Holds information **defined by the program**
  * `SHT_SYMTAB` A complete **symbol table** that provides symbols for link editing or dynamic linking \(for which it would have more symbols than necessary\)
  * `SHT_STRTAB` A **string table**, there can be multiples.
  * `SHT_RELA` **Relocation entries** with explicit addens, there can be multiples relocation sections
  * `SHT_HASH` A **symbol hash table** for ****dynamic linking
  * `SHT_DYNAMIC` Information for **dynamic linking**
  * `SHT_NOTE` This section holds **notes** \(ElfN\_Nhdr\)
  * `SHT_NOBITS` Like `SHT_PROGBITS`but occupies no space in the file
    * Although it contains no bytes, `sh_offset` contains the conceptual file offset
    * It can also have a nonzero size
  * `SHT_REL` **Relocation offsets**, there can be multiple relocation sections
  * `SHT_SHLIB` Reserved but unspecified semantics
  * `SHT_DYNSYM` A minimal set of **dynamic linking symbol**
* `sh_flags` A bit mask of flags
  * `SHF_WRITE` This section contains data that should be **writable** during process execution
  * `SHF_ALLOC` This section **occupies memory** during process execution
    * Some  sections do not reside in the memory image of an object file
  * `SHF_EXECINSTR` This section contains **executable** machine instructions
  * `SHF_MASKPROC` All bits included in this mask are reserved for **processor-specific** semantics
* `sh_addr` If this section appears in the memory image of a process, this var holds its **address**
* `sh_offset` The **byte offset** of the section
* `sh_size` The section's size in **bytes**
* `sh_link` A section header table **index** link, whose interpretation depends on the section type
* `sh_info` Extra information, whose interpretation depends on the section type
* `sh_addralign` Some sections have **address alignment** constraints
  * The value of `sh_addr` must be congruent to zero,  modulo the value of `sh_addralign`.  Only zero and positive integral powers of two are allowed.  The value 0 or 1 means that the section has no alignment constraints.
  * Only 0 and powers of 2 are allowed
  * 0 & 1 means no alignment is required
* `sh_entsize` Some sections hold a table of **fixed-sized entries**, this gives their size in bytes
* There's multiple possible sections :

<table>
  <thead>
    <tr>
      <th style="text-align:left">Name</th>
      <th style="text-align:left">Type</th>
      <th style="text-align:left">Attribute(s)</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b><code>.bss</code></b>
      </td>
      <td style="text-align:left"><code>SHT_NOBITS</code>
      </td>
      <td style="text-align:left"><code>SHF_ALLOC</code> &amp; <code>SHF_WRITE</code>
      </td>
      <td style="text-align:left">Uninitialized data, &quot;initialized&quot; to 0</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.comment</code>
      </td>
      <td style="text-align:left"><code>SHT_PROGBITS</code>
      </td>
      <td style="text-align:left"></td>
      <td style="text-align:left">Version control information</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.ctors</code>
      </td>
      <td style="text-align:left"><code>SHT_PROGBITS</code>
      </td>
      <td style="text-align:left"><code>SHF_ALLOC</code> &amp; <code>SHF_WRITE</code>
      </td>
      <td style="text-align:left">Initialized pointers to the C++ constructor functions</td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>.data</code></b>
      </td>
      <td style="text-align:left"><code>SHT_PROGBITS</code>
      </td>
      <td style="text-align:left"><code>SHF_ALLOC</code> &amp; <code>SHF_WRITE</code>
      </td>
      <td style="text-align:left">Initialized data</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.data1</code>
      </td>
      <td style="text-align:left"><code>Same</code>
      </td>
      <td style="text-align:left"><code>Same</code>
      </td>
      <td style="text-align:left">Same</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.debug</code>
      </td>
      <td style="text-align:left"><code>SHT_PROGBITS</code>
      </td>
      <td style="text-align:left"></td>
      <td style="text-align:left">Information for symbolic debugging, contents unspecified</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.dtors</code>
      </td>
      <td style="text-align:left"><code>SHT_PROGBITS</code>
      </td>
      <td style="text-align:left"><code>SHF_ALLOC</code> &amp; <code>SHF_WRITE</code>
      </td>
      <td style="text-align:left">Initialized pointers to the C++ destructor functions</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.dynamic</code>
      </td>
      <td style="text-align:left"><code>SHT_DYNAMIC</code>
      </td>
      <td style="text-align:left"><code>SHF_ALLOC</code> and sometimes <code>SHF_WRITE</code> (processor specific)</td>
      <td
      style="text-align:left">Dynamic linking information</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.dynstr</code>
      </td>
      <td style="text-align:left"><code>SHT_STRTAB</code>
      </td>
      <td style="text-align:left"><code>SHF_ALLOC</code>
      </td>
      <td style="text-align:left">
        <ul>
          <li>Strings needed for dynamic linking</li>
          <li>Most commonly the strings that represent the names associated with symbol
            table entries</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.dynsym</code>
      </td>
      <td style="text-align:left"><code>SHT_DYNSYM</code>
      </td>
      <td style="text-align:left"><code>SHF_ALLOC</code>
      </td>
      <td style="text-align:left">Dynamic linking symbol table</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.fini</code>
      </td>
      <td style="text-align:left"><code>SHT_PROGBITS</code>
      </td>
      <td style="text-align:left"><code>SHF_ALLOC</code> &amp; <code>SHF_EXECINSTR</code>
      </td>
      <td style="text-align:left">Executable instructions for process termination</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.gnu.version</code>
      </td>
      <td style="text-align:left"><code>SHT_GNU_versym</code>
      </td>
      <td style="text-align:left"><code>SHF_ALLOC</code>
      </td>
      <td style="text-align:left">Version symbol table</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.gnu.version_r</code>
      </td>
      <td style="text-align:left"><code>SHT_GNU_versym</code>
      </td>
      <td style="text-align:left"><code>SHF_ALLOC</code>
      </td>
      <td style="text-align:left">Version symbol needed elements</td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>.got</code></b>
      </td>
      <td style="text-align:left"><code>SHT_PROGBITS</code>
      </td>
      <td style="text-align:left">Processor-specific</td>
      <td style="text-align:left">Global offset table</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.hash</code>
      </td>
      <td style="text-align:left"><code>SHT_HASH</code>
      </td>
      <td style="text-align:left"><code>SHF_ALLOC</code>
      </td>
      <td style="text-align:left">Symbol hash table</td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>.init</code></b>
      </td>
      <td style="text-align:left"><code>SHT_PROGBITS</code>
      </td>
      <td style="text-align:left"><code>SHF_ALLOC</code> &amp; <code>SHF_EXECINSTR</code>
      </td>
      <td style="text-align:left">Executable instructions for initialization, it&apos;s executed before <code>main</code>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.interp</code>
      </td>
      <td style="text-align:left"><code>SHT_PROGBITS</code>
      </td>
      <td style="text-align:left">If there&apos;s a loadable segment, <code>SHF_ALLOC</code>
      </td>
      <td style="text-align:left">Pathname of am interpreter</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.line</code>
      </td>
      <td style="text-align:left"><code>SHT_PROGBITS</code>
      </td>
      <td style="text-align:left"></td>
      <td style="text-align:left">
        <ul>
          <li>Line numbers for symbolic debugging (describes the correspondence between
            the source and the machine code)</li>
          <li>Contents unspecified</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.note</code>
      </td>
      <td style="text-align:left"><code>SHT_NOTE</code>
      </td>
      <td style="text-align:left"></td>
      <td style="text-align:left">Various notes</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.note.ABI-tag</code>
      </td>
      <td style="text-align:left"><code>SHT_NOTE</code>
      </td>
      <td style="text-align:left"><code>SHF_ALLOC</code>
      </td>
      <td style="text-align:left">
        <ul>
          <li>Declare the expected run-time ABI of the ELF image</li>
          <li>May include the os name and its run-time versions</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.note.gnu.build-id</code>
      </td>
      <td style="text-align:left"><code>SHT_NOTE</code>
      </td>
      <td style="text-align:left"><code>SHF_ALLOC</code>
      </td>
      <td style="text-align:left">
        <ul>
          <li>Holds an ID uniquely identifying the contents of the ELF image</li>
          <li>Different files with the same build ID should contain the same executable
            content</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.note.GNU-stack</code>
      </td>
      <td style="text-align:left"><code>SHT_PROGBITS</code>
      </td>
      <td style="text-align:left"><code>SHF_EXECINSTR</code>
      </td>
      <td style="text-align:left">
        <ul>
          <li>Declare stack attributes, used by Linux object files</li>
          <li>Indicate to the GNU linker that the object file requires an executable
            stack</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.note.openbsd.ident</code>
      </td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left">OpenBSD native executables usually contain this section to identify themselves
        so the kernel can bypass any compatibility ELF binary emulation tests when
        loading the file</td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>.plt</code></b>
      </td>
      <td style="text-align:left"><code>SHT_PROGBITS</code>
      </td>
      <td style="text-align:left">Processor-specific</td>
      <td style="text-align:left">Procedure linkage table</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.rel&lt;name&gt;</code>
      </td>
      <td style="text-align:left"><code>SHT_REL</code>
      </td>
      <td style="text-align:left">If there&apos;s a loadable segment with relocation, <code>SHF_ALLOC</code>
      </td>
      <td style="text-align:left">
        <ul>
          <li>Relocation information</li>
          <li>The name corresponds to the one of the section the relocations applies
            to, e.g the relocation section for <code>.text</code> will be <code>.rel.text</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.rela&lt;name&gt;</code>
      </td>
      <td style="text-align:left"><code>SHT_RELA</code>
      </td>
      <td style="text-align:left">Same</td>
      <td style="text-align:left">Same</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.rodata</code>
      </td>
      <td style="text-align:left"><code>SHT_PROGBITS</code>
      </td>
      <td style="text-align:left"><code>SHF_ALLOC</code>
      </td>
      <td style="text-align:left">Read-only data that typically contributes to a nonwritable segment in
        the process image</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>rodata1</code>
      </td>
      <td style="text-align:left">Same</td>
      <td style="text-align:left">Same</td>
      <td style="text-align:left">Same</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.shstrtab</code>
      </td>
      <td style="text-align:left"><code>SHT_STRTAB</code>
      </td>
      <td style="text-align:left"></td>
      <td style="text-align:left">Section names</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.strtab</code>
      </td>
      <td style="text-align:left"><code>SHT_STRTAB</code>
      </td>
      <td style="text-align:left">If there&apos;s a loadable segment that includes the symbol string table, <code>SHF_ALLOC</code>
      </td>
      <td style="text-align:left">Strings, most commonly the strings that represent the names associated
        with symbol table entries</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.symtab</code>
      </td>
      <td style="text-align:left"><code>SHT_SYMTAB</code>
      </td>
      <td style="text-align:left">
        <p>If there&apos;s a loadable segment that includes the symbol table,</p>
        <p><code>SHF_ALLOC</code>
        </p>
      </td>
      <td style="text-align:left">Symbol table</td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>.text</code></b>
      </td>
      <td style="text-align:left"><code>SHT_PROGBITS</code>
      </td>
      <td style="text-align:left"><code>SHF_ALLOC</code> &amp; <code>SHF_EXECINSTR</code>
      </td>
      <td style="text-align:left">The &quot;text&quot;, or executable instructions, of a program</td>
    </tr>
  </tbody>
</table>

### String & Symbol table

* The object file uses strings to represent symbol and section names
  * These string are indexes in the string table
  * The first & last bytes of the string table are `\0`
* An object file's symbol table holds information needed to locate and relocate a program's symbolic definitions and references
* They're 2 versions of the structures, each for an architecture
  * The variables are the same, they're just in a different order

```c
 typedef struct {
   uint32_t      st_name;
   Elf32_Addr    st_value;
   uint32_t      st_size;
   unsigned char st_info;
   unsigned char st_other;
   uint16_t      st_shndx;
} Elf32_Sym;

typedef struct {
   uint32_t      st_name;
   unsigned char st_info;
   unsigned char st_other;
   uint16_t      st_shndx;
   Elf64_Addr    st_value;
   uint64_t      st_size;
} Elf64_Sym;
```

* `st_name` An index in the  string table representing the name of the symbol
  * If it's 0, it doesn't have one
* `st_value` The symbol's value
* `st_size` The symbol's size, 0 for no or unknown
* `st_info` The symbol's type & binding attributes :

  * Those keywords have associated numbers
  * Some range of numbers are reserved for processor specific stuff
  * `STT_NOTYPE` The type is not defined
  * `STT_OBJECT` Associated with a data object
  * `STT_FUNC` Associated with a function or other executable code
  * `STT_SECTION` Associated with a section
    * They exist for relocation
    * Binding : `STB_LOCAL`
  * `STT_FILE` The symbol's name gives the name of the source file associated with the object file
    * Binding : `STB_LOCAL` 
    * Section index : `SHN_ABS`
    * It  precedes the other  `STB_LOCAL` symbols of the file
  * `STB_LOCAL` Local symbols are not visible outside the object file containing their definition, there can be multiple of the same name among different files without problems
  * `STB_GLOBAL` Global symbols are visible to all object files being combined
  * `STB_WEAK` Weak symbols resemble global symbols, but their definitions have lower precedence
  * There are macros for packing and unpacking the binding and type fields:
    * `ELF32_ST_BIND(info), ELF64_ST_BIND(info)`  Extract a binding from an st\_info value
    * `ELF32_ST_TYPE(info), ELF64_ST_TYPE(info)` Extract a type from an st\_info value
    * `ELF32_ST_INFO(bind, type), ELF64_ST_INFO(bind, type)` Convert a binding and a type into an st\_info value
  * `st_other` Symbol visibility
    * `STV_DEFAULT` Default visibility rules
      * Global & weak symbols are available to other modules
      * References in the local module can be interposed by definitions in other modules
    * `STV_INTERNAL` Processor-specific hidden class
    * `STV_HIDDEN` Symbol is unavailable to other modules
      * References in the local module always resolve to the local symbol
    * `STV_PROTECTED` Symbol is available to other modules, but references in the local module always resolve to the local symbol
    * There are macros for extracting the visibility type : 
      * `ELF32_ST_VISIBILITY(other)`
      * `ELF64_ST_VISIBILITY(other)`
  * `st_shndx` The index of the symbol section in the section header table

### Relocation

* Relocation is the process of connecting a symbol's reference to a definition
* They describe how to modify their section content
* There's 4 structure : 32/64-bits with/without addend

```c
// Without addend

typedef struct {
   Elf32_Addr r_offset;
   uint32_t   r_info;
} Elf32_Rel;

typedef struct {
   Elf64_Addr r_offset;
   uint64_t   r_info;
} Elf64_Rel;

// With addend :

typedef struct {
   Elf32_Addr r_offset;
   uint32_t   r_info;
   int32_t    r_addend;
} Elf32_Rela;

typedef struct {
   Elf64_Addr r_offset;
   uint64_t   r_info;
   int64_t    r_addend;
} Elf64_Rela;
```

* `r_offset` The location at which to apply the relocation
  * For a relocatable file, the value is the byte offset from the start of the section to the storage unit affected by the relocation
  * For an executable file or shared object, the value is the virtual address of the storage unit affected by the relocation
* `r_info` The symbol table index and the type of relocation
  * Relocation types are processor-specific
* `r_addend` A constant addend used to compute the value to be stored into the relocatable field

### Dynamic type

* Structures relevant for dynamic linking

```c
typedef struct {
   Elf32_Sword    d_tag;
   union {
       Elf32_Word d_val;
       Elf32_Addr d_ptr;
   } d_un;
} Elf32_Dyn;
extern Elf32_Dyn _DYNAMIC[];

typedef struct {
   Elf64_Sxword    d_tag;
   union {
       Elf64_Xword d_val;
       Elf64_Addr  d_ptr;
   } d_un;
} Elf64_Dyn;
extern Elf64_Dyn _DYNAMIC[];
```

* `d_tag` controls the interpretation of `d_un`
  * There's a looot of d\_tag, so i won't list them
* `d_val` Various interpretations
* `d_ptr`  Program virtual addresses
  * When interpreting these addresses, the actual address should be computed based on the original file value and memory base address
* `_DYNAMIC` Array of all the structures in the `.dynamic` section
  * Automatically populated by the linker

### Notes

* The `.notes` section contains a series of structure, each followed by a name field and a descriptor field
* They're largely used by core files, but many projects also define their own set of extensions
* There's 2 structure, one for each architecture

```c
typedef struct {
   Elf32_Word n_namesz;
   Elf32_Word n_descsz;
   Elf32_Word n_type;
} Elf32_Nhdr;

typedef struct {
   Elf64_Word n_namesz;
   Elf64_Word n_descsz;
   Elf64_Word n_type;
} Elf64_Nhdr;
```

*  `n_namesz`The length \(with `\0`\) of the name field in bytes

  * The contents will immediately follow this note in memory

  `n_descsz` The length of the descriptor field in bytes

  * The contents will immediately follow the name field in memory

*  `n_type` Depending on the value of the name field, this will change. There's a lot of possible values, i won't list them

