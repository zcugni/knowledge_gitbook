# Wfuzz

## Presentation

* Versatile fuzzer for url parameters, post request data, directory name, cookies, etc
* It will fuzz places of the string where you wrote "**FUZZ**"
* It uses the **wordlist** specified with `-w <wordlist>`
  * Seclist has good ones
* If you're not directly fuzzing the **url**, specify it with `-u <url>`
* Syntax :
  * **URL fuzzing** : 
    * `wfuzz -w <wordlist> https://example.com/FUZZ.php`
    * `wfuzz -w <wordlist> https://example.com/listproducts.php?cat=FUZZ`
  * **POST request** :`wfuzz -w <wordlist> -u <url> -d "username=admin&pass=FUZZ"` 
    * `-d` Indicate the form data
  * Add **cookies** with `-b cookie1=value -b cookie2=value`
    * Cookies can also be fuzzed
  * Add **custom http headers with** `-H "myheader: headervalue" -H "myheader2: headervalue2"`
    * You can override existing headers
    * Headers can also be fuzzed
* Use a proxy with  `-p 127.0.0.1:8080`
* Not good with SSL or with website using CSRF tokens
* * You can filter result by status code or by :
  * `--hc <code>` / `--sc <code>` Hide / Show result with this status code
  * `--hc <nb>` / `--sc <nb>` Hide / Show result with this number of chars
  * `--hl <nb>` / `--sc <nb>` Hide / Show result with this number of lines
  * `--hw <nb>` / `--sw <nb>`Hide / Show result with this number of words
  * `--hh <nb>` / `--sh <nb>` Hide / Show result with this content-length
* Sometimes it takes an eternity to launch, i don't know why
* Use `'` instead of `"` around parameters to not have problems with the shell
* There's a lot more functionalities

## Sources

* Wfuzz's man page
* Wfuzz's [doc](https://wfuzz.readthedocs.io/en/latest/user/basicusage.html)

