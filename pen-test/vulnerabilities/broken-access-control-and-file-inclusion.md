# Broken Access Control & File Inclusion

## Concept

* Class of vulnerabilities that lets you access/use files/data/functions that you shouldn't be able to
* These attacks are quite similar, so I tends to see them as one class of vulnerabilities

## IDOR & Force Browsing

* IDOR stands for _I**nsecure Direct Object Reference**_
* These two happens when the app **directly expose sensible data without checking authorization**
  * Force Browsing is for **files** \(aka a backup file that you can access\)
  * IDOR is for **object reference** \(so ids, etc\) :  `http://website.com/orders?id=1213`

{% hint style="info" %}
Maybe i should add stuff about backup, extensions, etc
{% endhint %}

## MFLAC - _Missing Function Level Access Control_

* This happens when access control isn't done at the **function level**
* Given a function that changes a password and doesn't check that you have the right to do it \(because the web page does it beforehand\), you would be able ****to **forge a request and send it through a proxy**, bypassing the verification

## Directory Traversal & File Inclusion

* With _Directory Traversal_ is an attack where we can access \(and read/execute\) sensible files by moving inside directories with this kind of payloads : `../../../../etc/passwd`
* _Files Inclusion_ \(FI\) abuse arbitrary  `require` & `include` used in application
  * It 's a _**Local File Inclusion**_ \(LFI\) if you can only include local files \(through path traversal probably\)
  * It's a _**Remote File Inclusion**_ \(RFI\) if you can include any file
* You don't necessarily need to know the exact hierarchy of the directories, if you write too many `../../` it might still work
* Windows is more vulnerable than Linux because you can do `test/../../../file.txt` even if file.txt doesn't exist, meaning that if concatenation is done by the application, it won't break your payload
* Check if extension are added automatically and try to cut the string with `%00` if it's the case

## Sources

* [Owasp testing guide v4](https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v4.pdf)
* PwnFunction's [video](https://www.youtube.com/watch?v=rloqMGcPMkI)
* Misc Research

