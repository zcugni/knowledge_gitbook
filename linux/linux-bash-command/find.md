# Find

## Generalities

* Quite complete, i won't detail everything
* Basic syntax :`find <origin_path> [general_options] <search_options> [actions]`
* Use it with`2>/dev/null` \(because of all the Permission denied\)

## General options

* `-depth` / `-d` Process directory content before the directory itself \(implied by `-delete`\)
* `-maxdepth <nb>`

## Search options

* For every option with `nb`, we can also use `-nb` \(less than nb\) & `+n` \(more than nb\)
* For every option with `pattern`, those should be in `''` \(so the shell won't expand it\) and you can use `*` as wildcard
* Use `-not ...` for negation
* `-perm ...`
  * `-perm 644` Files with these exact permissions bits set
  * `-perm -220` / `-perm -u+w,g+w` Files with _at least_ these permissions bits set \(a 777 would match for example\)
  * `-perm /220` / `-perm /u+w,g+w` Files with any of these permissions bits set
* `-size <nb><unit>`
  * b : 512-byte blocks
  * c : Bytes
  * k : Kibibytes\(KiB\)
  * M : Mebibytes \(MiB\)
  * G : Gibibytes \(GiB\)
  * Example : `size +2G`
  * Size are rounded to the next unit
* `-type <letter>`
  * d : Directory
  * f : Regular File
  * l : symbolic link
  * s : socket

<table>
  <thead>
    <tr>
      <th style="text-align:left">Command</th>
      <th style="text-align:left">Effect</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><code>-user &lt;name&gt;</code>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-group &lt;name&gt;</code>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-name &lt;&quot;pattern&quot;&gt;</code> / <code>-iname &lt;&quot;pattern&quot;&gt;</code>
      </td>
      <td style="text-align:left">
        <ul>
          <li>Files which name matches the pattern,</li>
          <li>Iname is case-insensitive</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-path &lt;pattern&gt;</code> / <code>-ipath &lt;pattern&gt;</code>
      </td>
      <td style="text-align:left">
        <ul>
          <li>Files which path matches the pattern</li>
          <li>Ipath is case-insensitive</li>
          <li>The path is relative to the starting point of find</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-executable</code> / <code>-readable</code> / <code>-writable</code>
      </td>
      <td style="text-align:left">Files with these permissions for the current user</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-empty</code>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-anewer &lt;other_file&gt;</code> / <code>-newer &lt;other_file&gt;</code> / <code>-cnewer &lt;other_file&gt;</code>
      </td>
      <td style="text-align:left">Last accessed / modified / permission changed more recent than the other
        file</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>-regex &lt;regex_pattern&gt;</code>
      </td>
      <td style="text-align:left"></td>
    </tr>
  </tbody>
</table>

* \(1\) Fraction part are ignored, so `-mtime +1` would only show files accessed two or more days earlier

## Actions

* `-delete`
* `-ls` / `-fls <file>` Run `ls -dils` on files, -fls write the result to the specified file
* `-print0` / `-fprint0 <file>` Prints files name on the output \(or write it to the specified file\), with null characters separating them instead of new lines \(like in `-print`\), which make it less prone to breaking with filename containing white spaces
* `-printf <format>` / `-fprintf <file> <format>` Write the result to the specified file in the format describe in printf manner
* `-prune` If the file is a directory, do not descend into it
* `-exec` Execute commands on found files
  * Example : `-exec wc -l {} \;`
    * `{}` will be replaced by each instance, and `;` must be escaped because reasons
  * People often use a pipe with `xargs` but i don't know the details

## Operators

Expression will be abbreviated to expr

| Syntax | Meaning |
| :--- | :--- |
| `( expr )` | Priority |
| `! expr` | Not |
| `expr expr` / `expr -a expr` | And |
| `expr -o expr` | Or |

## Source

* find's man page

