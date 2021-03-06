# SMB

## Generalities

* Stands for _Server Message Block_
* It's a protocol used for sharing files, printer, serial ports, etc across a network
* There's a lot of different implementation
  * The _Common Internet File System_ \(CIFS\) is an old one, way too verbose, common at the time but not used anymore
  * The _Samba_ permit exchange with other OS \(SMB works on Windows initially\)
* It runs on port 445 via TCP
  * The older version runs over NetBIOS on port 139

## Shares

* IPC$ **:** With an anonymous null session you can access the IPC$ share and interact with services exposed via named pipes

## smbclient

* Part of the samba suite
* Lets you communicate with an SMB server
* `smbclient [options] //<server_netbios_name>[/service_name]`
  * The netbios name might be an ip
* `-L` List all available services
* For the password, you can either :
  * Give it in the command
  * Wait for a prompt
    * It will ask for one even if it's not needed, just hit enter if it's the case
  * Disable the prompt with `-N`

