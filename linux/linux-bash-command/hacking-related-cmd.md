# Hacking related cmd

## Hex dump

* There's multiple tools to see the **hex dump** of a file
* `xxd` My preferred one
  * `xxd <in_file> [out_file]` Dump it's hexa, with it's ascii equivalent
  * `xxd -r [-p] <in_file> <out_file>` Create the bytestream corresponding to an hex dump
    * `-r` Reverse
    * The `-p` is necessary to write the hex in a friendly manner \(**without offset**, etc\)
    * `<in_file>` can be set as the **standard input** with `-`
    * If you want to add content to that file after the new hex, just do `cat <other_file> >> <new_file>`
  * `-e` Switch to **little-endian**
  * `-b` Switch to **binary representation** \(`-r` doesn't work with it\)
* `hexdump <file>` Dump it's hexa, but i find the format less friendly \(it doesn't show the ascii equivalent and the endianness is inversed from `xxd`\)

## GPG

* pgp encoding
* `gpg --gen-key` Generate a key pair with default setting
* `gpg --export <username> > <file>` Export the public key of the user to a file \(binary format\)
  * To export the ascii representation of it, add `--armor`
* `gpg --import <file>` Import public key from file
* `gpg -r <username> --encrypt <file>` Encrypt file using user's public key \(binary format\)
  * To get an ascii file as result, add `--armor`
* `gpg --decrypt <file>` Decrypt file using your private key
* `gpg --list-keys` To see all the imported keys
* `gpg --fingerprint <username>` To get the fingerprint of the user's public key

## Object file analysis

### readelf

* `readelf <option> <elf file>` Display the content of elf object files
* Accepts 32 and 64 bits elf file as well as archives of elf files
* At least 1 option is necessary
* For elf files, its more precise than `objdump`
* Those are some of its interesting options, there's more :

<table>
  <thead>
    <tr>
      <th style="text-align:left">Option</th>
      <th style="text-align:left">Other aliases</th>
      <th style="text-align:left">Definition</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><code>-a</code>
      </td>
      <td style="text-align:left"><code>-all</code>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-h</code>
      </td>
      <td style="text-align:left"><code>--file-header</code>
      </td>
      <td style="text-align:left">Show the ELF header</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-l</code>
      </td>
      <td style="text-align:left">
        <p><code>--program-headers</code>
        </p>
        <p><code>--segments</code>
        </p>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-S</code>
      </td>
      <td style="text-align:left">
        <p><code>--section-headers</code>
        </p>
        <p><code>--sections</code>
        </p>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-s</code>
      </td>
      <td style="text-align:left">
        <p><code>--syms</code>
        </p>
        <p><code>--symbols</code>
        </p>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><code>--dyn-syms</code>
      </td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-e</code>
      </td>
      <td style="text-align:left"><code>--headers</code>
      </td>
      <td style="text-align:left">Show all headers, equivalent to <code>-h -l -s</code>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-r</code>
      </td>
      <td style="text-align:left"><code>--relocs</code>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-d</code>
      </td>
      <td style="text-align:left"><code>--dynamic</code>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-x &lt;number | name&gt;</code>
      </td>
      <td style="text-align:left"><code>--hex-dump=&lt;number | name&gt;</code>
      </td>
      <td style="text-align:left">Show the content of a given section (by number or name) in hexadecimal</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-p &lt;number | name&gt;</code>
      </td>
      <td style="text-align:left"><code>--strings-dump=&lt;number | name&gt;</code>
      </td>
      <td style="text-align:left">Show the content of a given section (by number or name) in printable chars</td>
    </tr>
  </tbody>
</table>

### objdump

* `objdump <option> <object file | object file archive>` Display the content of object files
* More general than `readelf`
* At least 1 option is necessary
* Those are some of its interesting options, there's more :

<table>
  <thead>
    <tr>
      <th style="text-align:left">Option</th>
      <th style="text-align:left">Other aliases</th>
      <th style="text-align:left">Definition</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><code>-a</code>
      </td>
      <td style="text-align:left"><code>--archive-headers</code>
      </td>
      <td style="text-align:left">If the file is an archive, prints its content like a <code>ls -l</code>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>--adjust-vma=&lt;offset&gt;</code>
      </td>
      <td style="text-align:left"></td>
      <td style="text-align:left">Add the offset to every address printed</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-C</code>
      </td>
      <td style="text-align:left"><code>--demangle</code>
      </td>
      <td style="text-align:left">Demangle (decode) low-level symbol names into user-level names, makes
        C++ functions readable</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-d</code>
      </td>
      <td style="text-align:left"><code>--disassemble</code>
      </td>
      <td style="text-align:left">Disassemble the sections that should contains instruction</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-g</code>
      </td>
      <td style="text-align:left"><code>--debugging</code>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-f</code>
      </td>
      <td style="text-align:left"><code>--file-headers</code>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-h</code>
      </td>
      <td style="text-align:left">
        <p><code>--section-headers</code>
        </p>
        <p><code>--headers</code>
        </p>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-j &lt;name&gt;</code>
      </td>
      <td style="text-align:left"><code>--section=&lt;name&gt;</code>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-r</code>
      </td>
      <td style="text-align:left"><code>--reloc</code>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-R</code>
      </td>
      <td style="text-align:left"><code>--dynamic-reloc</code>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-t</code>
      </td>
      <td style="text-align:left"><code>--syms</code>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-T</code>
      </td>
      <td style="text-align:left"><code>--dynamic-syms</code>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-x</code>
      </td>
      <td style="text-align:left"><code>--all-headers</code>
      </td>
      <td style="text-align:left">Print all headers information available</td>
    </tr>
  </tbody>
</table>

## Misc

* Tracing :
  * `strace` Intercepts system calls made by libs to the kernel
  * `ltrace` Intercepts library & system calls made by your application to libs
* Debugging :
  * `ulimit -c unlimited` Tell the OS to dump the memory whenever a crash occurs \(must be root\) 
* Base 64 encoding :
  * `base64 -d <file>` Decode
    * To do it from a file use `echo <file_name> | base64 -d`

