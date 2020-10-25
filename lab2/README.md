
## Exercise 1

### Hub:
- Layer3:
1. The Ping process starts the next ping request.
2. The Ping process creates an ICMP Echo Request message and sends it to the lower process.
3. The source IP address is not specified. The device sets it to the port's IP address.
4. The device sets TTL in the packet header.
5. The destination IP address is in the same subnet. The device sets the next-hop to destination.
- Layer2:
1. The next-hop IP address is a unicast. The ARP process looks it up in the ARP table.
2. The next-hop IP address is not in the ARP table. The ARP process tries to send an ARP request for that IP address and buffers this packet.

### Switch
- Layer3:
1. The Ping process starts the next ping request.
2. The Ping process creates an ICMP Echo Request message and sends it to the lower process.
3. The source IP address is not specified. The device sets it to the port's IP address.
4. The device sets TTL in the packet header.
5. The destination IP address is in the same subnet. The device sets the next-hop to destination.
- Layer2:
1. The next-hop IP address is a unicast. The ARP process looks it up in the ARP table.
2. The next-hop IP address is in the ARP table. The ARP process sets the frame's destination MAC address to the one found in the table.
3. The device encapsulates the PDU into an Ethernet frame.
- Layer1:
1. The port FastEthernet0 is sending another frame at this time. The device buffers the frame to be sent later.

Both switch and hub uses third layer of OSI stack;
Difference: 
- For hub, the ARP process tries to send an ARP request for that ip address and buffers this packet, but for the switch, the IP address is
in the ARP table. The ARP process sets the de destinationt MAC to that found in the ARP table.

- switch:
  - switch ul stie cand ajunge un pachet cu destinatia mac ul respectiv trimite pachetul pe port-ul respectiv
  - el hotaraste pe unde sa il trimita
  - daca nu exista o intrare in tabela pentru acel mac, sw-ul o sa faca broadcast la pachete
- HUB
  - HUB-el este efectiv un convertor. El nu se gandeste la ce o faca cu pachetele, el doar expandeaza domeniul de coliziune. Ia pachetul si il  
    - trimite pe toate celelalte porturi, este treaba calculatorului sa vada daca este pentru el pachetul 

### Tabela cam(comutare):

- coloana mac si coloana port:
- cand se deschide sw, el nu are nimic trecuta in tabela
- nu se poate ca in tabela sa fie un mac si mai multe porturi asociate


## Exercise 2

- Apare o singura intrare deoarece adresa mac a lui PC1 nu poate aparea de doua ori in tabela
- Trimitere de la PC2 la PC3 - apare inca o intrare in tabela pentru PC3

## Exercise 3

- 