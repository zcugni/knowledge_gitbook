# Binary, hex & bytes

## Terminology

* A `bit` is the base unit in computer science
  * 4 bits are a `nibble` or a half-byte
  * 8 bits are a `byte` \(`octet` in french\)
  * 16 bits are a `word`
  *  32 bits are a `double word` \(dword\)
  * 64 bits are a `quad word` \(qword\)
* A byte is read the same on every machine
  * How it's written in binary \(without spaces normally\) : 0 0 0 0 0 0 0 0
  * The decimal value of each position : 128 64 32 16 8 4 2 0
  * The power of 2 of each position : 2^7 2^6 2^5 2^4 2^3 2^2 2^1 2^0
  * There's 256 value, so the max value of an unsigned byte is 255 \(0-255\)

## Endianness

* Some data needs multiples bytes to be encoded
  * For a 2-byte data, the highest byte is x 256 to get the actual value it represent
* The order in which they're read isn't the same on every machine \(this is called `endianness`\) :
  * `big-endian` machine consider the highest byte is at the left-most position
  * `little-endian` machine consider the highest byte is at the right-most position
* The problem that arise when 2 machines using different endianness communicate is called the _NUXI_ problem
  * Example : given the same value UNIX, written as 2 short \(2 pairs of bytes\)
  * Big-endian machine will understand UNIX
  * Litlle-endian machine NUXI
* Solutions :
  * Network protocol agreed to use the big-endian way, so there's functions to convert the data to this format and back to the format machine
  * For other communications, a `BOM` \(Byte Order Mark\) is used. 
    * It's a magic number, such as 0xFEFF, that indicates in which order are the bytes.
    * If you read 0xFFFE, it means the data are ordered in the opposite way and that you need to convert them
    * This solution means you need to send traditional data each type
* Advantages of each format :
  * Big-endian is read in the same way we would \(at least in occidental languages\)
  * Little-endian lets you directly access the lowest byte, and so rapidly check if it's an even or odd number \(for example\)

## Hex

* Hex is base-16
* The representation of it's number is as follows : 0 1 2 3 4 5 6 7 8 9 A B C D E F
* Hex is often used in computer science because a nibble \(4 bits\) has 16 potential combination of 0 & 1 \(ranging from 0000 to 1111\). Meaning we can represent each of them by an hex "number"
* A byte is often written as a combination of 2 hex number \(aka `FF`, `2A`, etc\)  because it's way more readable than binary
