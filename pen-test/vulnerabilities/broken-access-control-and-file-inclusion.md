# Broken Access Control & File Inclusion

## Concept

* Class of vulnerabilities that lets you access/use files/informations/functions that you shouldn't be able to.
* Les nuances entre chaque attaque sont assez minimes donc j'ai un peu tendance à toutes les regroupées en 1 seul concept.

## IDOR -  _Insecure Direct Object Reference_

The application directly expose reference to an object \(user details, etc\) without checking authorization : [http://website.com/orders?id=1213](http://website.com/orders?id=1213)

De ce que j'ai compris, ça sous-entends généralement l'utilisation d'un id, etc, comparé au forced browsing.

## MFLAC - _Missing Function Level Access Control_

This happens when an access control isn't done at the function level, meaning that if you have this link : [https://website/changepwd?id=dasfaf-rwtwt-864864864](https://website/changepwd?id=dasfaf-rwtwt-864864864)

You can give it the id of another user and the function will execute none the less.

De ce que j'ai compris, la diff avec IDOR c'est que même si la page en elle-même a des protections, si on fait directement une requête forgée de toute pièce, celle-ci sera acceptée.

## Forced Browsing

The application directly expose sensible files \(disclosing information, backup/old files with vulnerabilities corrected in newer versions, etc\).

## Directory Traversal & File Inclusion

* With _Directory Traversal_ we can access \(and read/execute\) sensible files by moving inside directories with this kind of payloads : `../../../../etc/passwd`
* _Files Inclusion_ abuse arbitrary "require/include" used in application
  * LFI \(local\) can only include local files \(through path traversal probably\)
  * RFI \(remote\) can include any file
* Bypass of checks :
  * If file type \(aka **mime type**\) is deduced from the magic number, add arbitrary ones that are accepted at the start of the file
  * If `.php` is not accepted, you can use `.php3`
  * Certaine config d'apache permettent d'exécuter des fichiers ayant ce genre d'extensions `.php.jpg` ce qui peut être pratique pour bypass des vérifs de sécu
* On a pas besoin de connaitre la structure exacte des dossiers car même si on mets trop de `../../` ça marchera quand même.
* Windows est plus vulnérable que linux car on peut faire ça `test/../../../file.txt` même si _test_ n'existe pas, ce qui facilite des cas où le chemin est concaténé à autre chose.
* Check if extension are added programmatically and try to cut the string with `%00` if it's the case

## Sources

* [Owasp testing guide v4](https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v4.pdf)
* PwnFunction's [video](https://www.youtube.com/watch?v=rloqMGcPMkI)
* Misc Research

