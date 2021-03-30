# Wfuzz

## Presentation

Versatile fuzzer \(brute-forcer\) for :

* Directory/file names
* URL parameters
* POST request data
* Cookies
* HTTP Headers
* etc

## Usage

* It will fuzz places of the string where you wrote "**FUZZ**"
* Syntax : 
  * `wfuzz -w <wordlist> [options] <url>`
  * `-w` Indicates the **wordlist**, ****[Seclist](https://github.com/danielmiessler/SecLists) has multiples
  * If the url isn't last in order, you can specify it with `-u <url>`
* Options :
  * `-d <post_data>` Specifies the data to send with a **POST request**
    * For example : `-d "username=admin&pass=FUZZ"`
  * `-b cookie=value` Specify a **cookie** \(repeat for multiple cookies\)
  * `-H "header: value"` Specify an **HTTP header** \(repeat for multiples headers\)
    * You can override existing headers with it
  * **Filter** **result**  :
    * `--hs <"regex">` / `--ss <"regex">` Hide /Show result with this regex in the content
      * \(It can be as simple as "Invalid username"\)
    * `--hc <code>` / `--sc <code>` Hide / Show result with this **status code**
    * `--hc <nb>` / `--sc <nb>` Hide / Show result with this **nb of chars**
    * `--hl <nb>` / `--sc <nb>` Hide / Show result with this **nb of lines**
    * `--hw <nb>` / `--sw <nb>`Hide / Show result with this **nb of words**
    * `--hh <nb>` / `--sh <nb>` Hide / Show result with this **content-length**
  * Use a **proxy** with  `-p 127.0.0.1:8080`
* **Not good with SSL or with website using CSRF tokens**
* Sometimes it takes an eternity to launch, i don't know why
* Use `'` instead of `"` around parameters to avoid problems with the shell
* There's a lot more functionalities

## Sources

* Wfuzz's man page
* Wfuzz's [doc](https://wfuzz.readthedocs.io/en/latest/user/basicusage.html)

