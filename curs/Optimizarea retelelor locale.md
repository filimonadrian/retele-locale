# Optimizarea retelelor locale

## Probleme in LAN-uri

- EFICIENTA 
  - intr-o retea cu multe switch uri, un broadcast poate fi foarte  costisitor
- ADMINISTRARE
  - cu cat reteaua e mai mare, cu atat este mai greu de gestionat problema
- CALITATE
  - download de streaming /voce vs fisier:
  - daca se pierde un pachet de streaming nu este o problema, nu ai nevoie de retransmisii
  - o intarziere in retea(buffering) poate duce la intreruperi
  - trebuie sa tratam prioritar traficul
  - !!! QOS !!!
  - cel mai important cost in retea este costul de latenta
- COST 

## SOLUTIA - VLAN

- pot sa zic ca primele 8 port-uri ale unui switch sunt in aceasta retea
  - si urmnatoarele 8 sunt in alta retea de broadcast(adica le virtualizez)
  - astfel o sa am o limitare(virtuala) a bradcastului
- De ce nu se alege un router? 
  - Costul unui router este mai mare
    - chiar si asa, comunicatia intre orice interfete va fi mai lenta pentru ca routerul este mai destept 
      - si face mai multe operatii
      - 
## VLAN:

- nu voi mai putea comunica cu porturi aflate in alt VLAN
- este un numar pe 12 biti(1 --> 4096)
- VLAN_ID 1 este cel implicit(un switch scos din cutie va avea toate porturile definite pe acelasi VLAN)
- un port poate apartine unui singur VLAN (acesta se numeste ACCESS PORT)
- TRUNKING
  - este o multiplexare!!
  - porturile nu functioneaza doar ca AP, ci si ca TRUNK PORTS
  - o sa am o legatura care poate transporta oricare dintre VLAN uri

### Formatul 802.1q

- formatul ethernet: addr dest, addr src, LUNGIME-TIP, DATE, FSC
- pentru a retine informatia de VLan se introduce un nou camd din 4 octeti: 802.1q tag
- pachet ethernet care contine si formatul 802:  addr dest, addr src, !!!802.1Q TAG !!, Lungime/tip, DATE, FSC

- Cele 2 functii ale switch ului: comutarea de pachete si invatarea(creearea tabelei)
- un switch mai prost cauta doar in anumite locuri in antet()
  - unul mai destept va cauta si in mijloc si actualizeaza/citeste cine este acel 802.1q 
  - practic nu toate switch urile o sa citeasca aceasta informatie

### VLAN nativ

- daca am un cadru care nu are eticheta nu stiu cui apartine
- de aceea am un singur VLAN nativ(default)
- voi configura pentru ce vlan nu mai este nevoie ca sw sa imi faca ethichetarea pachetelor  
- PENTRU PACHETELE CU VLAN NATIV NU AM NEVOIE SA MAI PUN .1Q IN CADRU

### ROUTER

- am nevoie de un router ca sa trec intre domenii de broadcast
- se rescrie antetul de nivel 2
- switchul va sti apoi pe ce domeniu de broadcast sa trimita

### SOLUTIA CLASICA

- nu trebuie sa stiu alte informatii despre antete
- dezavantaje: numar foarte mare de interfete

### SOLUTIA ROUTER-on-a-stick

- se foloseste o singura interfata fizica
- interfata fizica - sparta in subinterfete logice

- AVANTAJE: ma o singura interfata( nu am cate o interfata pentru fiecare vLAN)
- DEZAVANTAJE: poate sa fie un punct de congestie pentru reteaua noastra

## Spanning Tree Protocol (STP)

- daca o sa pice o legatura, dorim sa avem totusi comunicatia
- trebuie sa avem  REDUNTANTA pentru a fi safe

### Probleme daca o sa am conectivitate totala(toate switch urile conectate)

- TTL-ul asigura ca daca un pachet este prins intr-o bucla, ttl-ul se face 0 si pachetul va fi aruncat
- acest ttl este parte dintr-un antet pentru care se calculeaza CRC(aceasta este adevarata problema a TTL-ului)

### Probleme aduse de reduntanta

- Bucla (avanlansa de broadcast)
- pachetele vor fi livrate de un numar nelimitat de ori
- coruperea tabelelor de comutare:

## STP

- porturile vor trece prin 3 fazE: 
  - designated port - trimite si primeste
  - root port - acesta niciodata nu va fi inchis, calea cea mai eficienta spre root
  - blocked port - port blocat

- se folosesc costuri. Acestea sunt legate de latimea de banda
- daca sunt sw-uri foarte rapide, costurile vor fi exponentiale(se prefera o latime de banda cat mai mare)
  
1. Bridge id
   - foecare sw are un id unic
   - valoare pe 64 bits
     - 16 bits prioritatea
     - 48 adresa MAC
     - prioritatea este implicit 32768
     - sw cu BID va avea prioritatea cea mai mare
2. BPDU
   - mesajele folosite de stp pentru a comunica informatii intre bridge uri
   - se face o transmisie o data la 2 secunde
   - informatii transmise: 
     - root bridge id
     - cost pana la root bridge
     - BID
     - port ID
   - in primul pachet bridge id == root id(costul o sa fie 0)

3. Pasii STA
   - aleregea root bridge
   - alegerea unui root port
   - alegerea designated ports
   - alegerea si inchiederea blocked ports
   - O LEGATURA NU SE INMCHIDE NICIODATA IN AMBELE PARTI

### PASII

1. Alegerea Root Bridge 
    - cazul 1: toate au aceleasi prioritati 
      - se alege mac-ul cel mai mic(o comparatie de valori)
    - cazul 2: prioritati diferite
      - se alege cel cu prioritatea cea mai mica( care au prioritate egala se compara dupa MAC)
2. Root PORTS
3. ROOT PORTS 
   - Fiecare switch non-root trebuie sa aiba un root port
4. Blocked Ports
   - se blocheaza din porturi
   - atunci cand crapa chestii, incep sa se deschida porturi
     - UN PORT BLOCAT NU TRIMITE, DAR ASCULTA. ASA IMI DAU SEAMA CA CEVA A CRAPAT
     - sunt niste pachete speciale ale protocolului pe care le asculta

- se pastreaza portul mai mic al sw-ului de upstream(nu cel local)
