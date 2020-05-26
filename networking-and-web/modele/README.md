# Modèle & Protocoles

## Généralités

* Technos divisées en couche
* Si une couche inférieur ne fonctionne pas, celle du dessus non plus
* Chaque couche ne communique qu'avec celles adjacentes
* 2 modèles
  * _Open System Interconnect_ \(OSI\) : 7 couches, détaillé mais peu représentatif
  * TCP/IP : 5 couches, détaillé ici

## Communication entre couche

Chaque "paquet" d'information \(sens global du terme\) est composé de l'header du protocol + son payload. Pour passer d'une couche à l'autre, les informations sont encapsulée et le combo devient le payload de la prochaine couche.

![](../../.gitbook/assets/encapsulation.png)

Les informations de la data link \(la mac adresse\), est modifiée à chaque hop, tandis que celle de l'ip de destination reste pour tout le trajet.

## Troubleshooting

| Layer | Tools |
| :--- | :--- |
| Physical | link light, ipconfig/ifconfig, cable replacement |
| Datalink | arp, ND, tcpdump |
| Network | ping, traceroute |
| Transport | netstat, netcat, tcpdump |
| Application | Logs, debuggers |

## Sources

* _Networking for Systems Administrators_ by Michael W. Lucas
* [Techninjistics](https://www.youtube.com/user/techninjistics)
* Pentesterlab
* Misc Research

