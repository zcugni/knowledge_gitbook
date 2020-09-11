# SMB

## Generalities

* Stands for _Server Message Block_
* It's a protocol used for sharing files, printer, serial ports, etc across a network
* There's a lot of different implementation
  * The _Common Internet File System_ \(CIFS\) is an old one, way too verbose, common at the time but not used anymore
  * The _Samba_ permit exchange with other OS \(SMB works on Windows initially\)
* It runs on port 445 via TCP
  * The older version runs over NetBIOS on port 139

