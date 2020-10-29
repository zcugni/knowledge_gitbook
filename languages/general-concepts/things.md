# Linker, Interpreter, etc

## Object file

* Object code refers to the corresponding binary of a source code
  * It's relocatable and needs to be linked before being executable
* Assemblers and compilers can generate object files
* There's different format, but the main one is **Executable Linking Format** \(ELF\)

## ELF

* An elf file isn't necessarily a relocatable object file, it can also be an executable, a core file or a shared object
* An ELF file is composed of :
  * An header
  * An optional program header \(for executable and shared object files\)
  * An optional section header \(for linking\)
* * * program header ?
  * The bytes from the file are mapped to the beginning of the memory segment

### Header

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
  * **The value 0 is often use for non-acceptable values, so i won't detail them**
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
  * `SHF_WRITE` This section contains data that should be writable during process execution.
  * `SHF_ALLOC` This section occupies memory during process execution
    * Some  sections do not reside in the memory image of an object file
  * `SHF_EXECINSTR` This section contains executable machine instructions
  * `SHF_MASKPROC` All bits included in this mask are reserved for processor-specific semantics
* `sh_addr` If this section appears in the memory image of a process, this var holds its address
* `sh_offset` The byte offset of the section
* `sh_size` The section's size in bytes
* `sh_link` A section header table index link, whose interpretation depends on the section type
* `sh_info` Extra information, whose interpretation depends on the section type
* `sh_addralign` Some sections have address alignment constraints
  * The value of `sh_addr` must be congruent to zero,  modulo the value of `sh_addralign`.  Only zero and positive integral powers of two are allowed.  The value 0 or 1 means that the section has no alignment constraints.
  * Only 0 and powers of 2 are allowed
  * 0 & 1 means no alignment is required
* `sh_entsize` Some sections hold a table of fixed-sized entries, this gives their size in bytes

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
      <td style="text-align:left"><code>.bss</code>
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
      <td style="text-align:left"><code>.data</code>
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
      <td style="text-align:left"><code>SHT_PROGBITS</code>
      </td>
      <td style="text-align:left"><code>SHF_ALLOC</code> &amp; <code>SHF_WRITE</code>
      </td>
      <td style="text-align:left">Initialized data</td>
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
      <td style="text-align:left">.gnu.version</td>
      <td style="text-align:left"><code>SHT_GNU_versym</code>
      </td>
      <td style="text-align:left"><code>SHF_ALLOC</code>
      </td>
      <td style="text-align:left">Version symbol table</td>
    </tr>
    <tr>
      <td style="text-align:left">.gnu.version_r</td>
      <td style="text-align:left"><code>SHT_GNU_versym</code>
      </td>
      <td style="text-align:left"><code>SHF_ALLOC</code>
      </td>
      <td style="text-align:left">Version symbol needed elements</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>.got</code>
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
      <td style="text-align:left"><code>.init</code>
      </td>
      <td style="text-align:left"><code>SHT_PROGBITS</code>
      </td>
      <td style="text-align:left"><code>SHF_ALLOC</code> &amp; <code>SHF_EXECINSTR</code>
      </td>
      <td style="text-align:left">Executable instructions for initialization, it&apos;s executed before <code>main</code>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">.interp</td>
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
  </tbody>
</table>





## Assembler

* Transform assembly code into machine code \(aka binary\), creating object files

## Compiler

* See [Compilers](https://zcugni.gitbook.io/notes/languages/compilers)

## Interpreter

* An interpreter, like a compiler, translates high-level language into machine code \(binary\)
* Instead of reading the whole source code at once like a compiler, it reads it one line at a time \(and execute it\)
  * It stops as soon as an error occurs and report it

## Loader

* Used to load an executable file into memory, initialize the registers and execute it

## Pre-processor

* Often the first step of compilation
* Interpret pre-processor instructions such as includes and defines
  * By replacing includes with the desired content and macros by their value
* Removes comments, combined multi-line instruction and other clean-up stuff

## Makefile

{% hint style="info" %}
Add
{% endhint %}

## Sources

* [Tutorialspoint](https://www.tutorialspoint.com/compiler_design/index.htm)

