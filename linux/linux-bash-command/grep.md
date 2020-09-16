# Grep

## Généralités

* Permet de trouver des strings dans un input
* `grep <pattern> <file> <file>`
* Pour chercher sur les noms de dossier, utiliser `find ... | grep ...`
* On peut choisir différent mode pour le regex, mais celui de base semble globalement convenir

## Options

* `-f <file>` Récupère les patterns à chercher depuis le fichier
* `-e <pattern>` Permet de spécifier un pattern
  * S'il n'y en a qu'un, on a pas besoin de mettre `-e`
* `-i` Case-insensitive
* `-v` Invert match \(shows lines that are not matching\)
* `-w` / `-x` Only match whole words / lines
* `-c` Replace output with a count of matches
* `-m nb` Stop after nb matches
* `-n` Prefix each lines in result with a number
  * `-T` Align result \(useful when use with `-n` or other prefixing options\)
* `-Z` Replace newline character by null byte \(usefull when dealing with filenames containing new lines\)
* `-A <nb>` / `-B <nb>` / `-C <nb>` print nb lines of context after / before / around the match
* On peut exclure des fichiers/dossiers/etc mais 
* `-r` Recursive

## Source

* grep's man page

