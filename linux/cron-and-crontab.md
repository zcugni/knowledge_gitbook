# Cron & Crontab

## Generalities

* Lets you plan cmd execution
* Can mail or log the result
* Widespread use

## **Commands**

* `crontab -l` List your crontab 
* `crontab -e` Edit your crontab 
* `crontab -u <username>` \(As root\) Select crontab of another user
* `crontab -r` Remove your crontab

## **Format** 

* `<min> <hour> <month_day> <month> <week_day> <cmd>`
  * Min : 0-60
  * Hour : 0-23
  * Month's day : 1-31 or 3-letters words \(case-insensitive\)
  * Month : 1-12 or 3-letters words \(case-insensitive\)
  * Day of week : 0-7 \(0 & 7 are Sunday\) or 3-letters words \(case-insensitive\)
* Jobs written in the system crontab have an extra field before the cmd for the user id
* `# This is a com` If it's written after a valid cron line, it will be considered part of the cmd launched, not a com of the line
* We can change the environment variable with `name = value`
  * By writing `$PATH = /usr/local/bin`, the following lines will use this value.
  * Caution, `$` & `~` aren't parsed
* Special char/words are accepted :
  * `*`
  * `@reboot`
  * `@yearly`
  * `@monthly`
  * `@weekly`
  * `@daily`
  * `@hourly`
* Ranges of numbers can be used & are inclusive
* List are allowed, comma-separated. Lists of ranges are possible.
* Steps are possible : `*/2` in the day field means "every other day"
* In the cmd  `%` will be changed into newlines character, and anything following will be sent as standard input.

## **Example**

* `15 10 * * 3-5 echo "test" >> /home/lyssei/test.txt`
* `15 10 1-10/2 * 5 cmd` At 10h15 every fifth day of the week & for day 1-10 of the month, un jour sur deux
* `15 10 1,2,3,7 * 5 cmd` At 10h15 every fifth day of the week and the 1,2,3 & 7th day of the month

## **Files**

* You shouldn't edit them directly, use `crontab` instead
* `/var/spool/cron/crontabs/` User's crontab
* `/etc/crontab` System crontab, can only be edited by root
  * In Debian, it's configured to run all exe owned \(and only writable by\) root in `/etc/cron.hourly/`, `/etc/cron.daily/`, `/etc/cron.weekly/` and `/etc/cron.monthly/`
* `/etc/cron.d/` Program specific crontab
* `/etc/cron.allow` Whitelist user who can use `crontab`, `/etc/cron.deny` blacklist them. If the 2 are present, `.allow` is used

## Sources

* man pages

