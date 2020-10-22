# Protocolul IP

Ce problema rezolva ip-ul? 
- vrea sa rezolve problema transmiterii in retea, ierarhizat
- fara IP ar fi trebuit sa avem conectate toate calculatoarele cu toate calculatoarele
-!! - Este o forma de identificare a sursei si a destinatiei

Ce problema rezolva TCP?  
- siguranta transmiterii (aspect esential), asigura o confirmare
- TCP isi da seama de ordinea pachetelor
- controlul fluxului

TCP si UDP: in comnun: 
- ambele folosesc porturi si fac multiplexare(fac conversatii in paralel si stiu sa le 
descifreze la destiatie)
- pot sa definesc mai multe subiecte de discutie
- mai multe conversatii pe un singur canal(care este definit de IP)

Nivelul retea vine cu o problema:
- avem identificare unica a sursei si destinatie, dar mac-ul nu permite o 
relatie de ordonare(cautarea se face itnr-o multime neordonata)
- ip-ul este ca un serviciu postal, totul este ierarhic

Functiile nivelului retea: 
- adresare globala - IP(fiecare dispozitiv este identificat in mod unic)
- comunicatie end-to-end
- Rutare(dirjare)

Protocoale de nivel retea: 
- IPv4/IPv6 
- ip-ul este un protocol best effort - ai 0 garantii
- ICMP - garanteaza ca te notifica atunci cand apare o problema

Protocolul IPv4  
- are aproape 40 de ani
- Broadcast: toata lumea din retea primeste acelasi lucru
- Multicast: statiile care apartin unui grup primesc o copie a pachetului destinat grupului

## Authority
- IANA: adresele sunt distribuite de niste agentii regionale: IANA
- RIPE: zona care ne administreaza si pe noi

## Formatul antetului(Overhead-ul)
Cata informatie adaugam si de ce?  
- Cea mai mare problema IP este cea a campului OPTIONS(optiuni)
- pana la acest camp avem 20 de octeti
- ipv4 are intre 20-60 octeti
- Aceste este cea mai mare problema. Performantele scad de zeci de ori deoarece inca nu sunt echipamente care sa citeasca eficient asta
- campul de options este inutil astazi
- overhead: ethernet 18 octeti, TCP si IPv4 20 octeti, IPv6 40 octeti

# IP
- TTL - counter ca sa evite buclele de rutare
  - TTL face parte din antetul care trebuie sa fie inclus in CRC(numar pe 16 biti => 16 impartiri)
  - la fiecare decrementare a pachetului trebuie sa recalculez CRC(sa fac din nou cele 16 impariri)
  - nu mai exista erori in nucleului internetului, CRC-ul este calculat inutil in zilele noastre 

- ADRESA IPv4 este formata din 4 octeti
- esenta adresarii IPv4 este viteza(ierarhizarea)
- prima pare care este vazuta de routere(a doua parte este ignorata de router), a doua parte vazuta doar de reteaua locala

- Cati dintre cei 32 biti sunt de retea? 
  - Masca de retea: sir de 32 biti, primii de 1, restul de 0
  - /24 -- masca de reatea are primii 24 de biti diferiti de 0(pentru statie am 8 biti)
  - O adresa IP fara masca poate fi de broadcast, de retea...etc
  - pentru o reprezentare corecta trebuie sa punem si `MASCA`!
  - 
## Adresa de retea

- bitii de retea sunt comuni, in partea de statie 
- adresa de retea nu apartine nimanui
- adresa de broadcast e a tuturor - se face un OR intre inversa mastii

## Adresa de loopback

- este folosita pentru validarea procesului de incapsulare, a kernelului
  
## Greseli

- toti bitii de statie sunt 1 ==> adresa de broadcast, nu pot fi alocate unei statii
- toti bitii de statie sunt 0 ==> adresa de retea, nu poate fi alocata unei statii
- pentru broadcast: sursa este adresa sursei, destinatia este adresa de retea si toti bitii de host sunt 1
  
## Clase de adrese

- A /8, B/16, C/24
- D - Multicast
- E - experimental 

## Adrese publice si private

- RFC1918
- s-au definit niste spatii de adrese care apartin tuturor, nu doar companiilor private
- in internet fiecare sursa trebuie sa fie unica, de aceea folisim o translatare: `NAT`

## Subnetare
- Cum dintr-un spatiu de adrese pot sa fac mai multe retele(sa fac mai multe spatii de retele valide) 
  - Intr-o firma mare exista un spatiu de adrese pentru fiecare departament
  - o retea este un domeniu de broadcast
  - daca cineva intra in domeniul de broadcast poate sa faca DDOS
- `beneficii subnetare`:
  - eficienta
  - SECURITATE
  - fiecare dintre suberetele este un domeniu separat de broadcast si pot sa fac tehnici de aparare
  - in acelasi domeniu de broadcast NU POT SA APLIC TACTICI DE SECURITATE
- exemplu1: daca am o adresa cu masca de 16 biti, am 2^16 - 2(retea si broadcast)
  - pot sa mai pun 2 biti la retea si sa am 4 retele diferite, 4 * (2^14 - 2)
- 2 routere formeaza si ele un domeniu de broadcast!(sunt si ele o retea)
- exemplu2:
- VLSM - variable length subnet mask(subnetare cu masca variabila)

## Dezavantaje IPv4

- adrese insuficiente
- antet complicat( alte 5 campuri care nu mai sunt utilizate)
- Nu suporta pachete de dimensiuni foarte mari(are overhead de 20 octeti la 1500 octeti de date)
- NU SE POATE IMPLEMENTA MULTICASTUL
- NAT introduce multe probleme(pieredre de conectivitate)

# Protocolul ARP(Address Resolution Protocol)

- foarte problematic, important pentru functionarea IP
- Pentru Ip-ul pe care il am(printr-o interogate de DNS), ARP imi da adresa MAC(pentru antetul de nivel 2)
- RARP( reverse ARP) - dau un mac, primesc un IP
- DHCP este o generalizare a RARP

- stiu adresa IP, fac un BROADCAST cu cererea IP(Ce MAC are aceasta adresa IP?)
- fiecare statie isi verifica IP-ul si daca o sa corespunda, statia trimite un `raspuns ARP`
- asocierile se pastreaza intr-o `Tabela ARP`

- cand comunic in afara retelei:
  - Solultia 1: `Proxy ARP`
    - se face in general pentru senzori si chestii mici
    - imi imaginez ca totul este o retea mare
    - Cand trebuie sa trimit un pachet la un server configurat la nivelul unui senzor, intreb ca si cum sunt intr-o mare retea locala
    - Daca routerul care primeste acest pachet este de ajutor(ruleaza acest proxy arp): daca acesta cerere este in reteaua de broadcast, atunci sa raspunda
    - Daca nu, routerul `minte` ca el are acea adresa mac, dar de fapt routerul o sa trimita mai departe
    - aceasta asociere este o asociere virtuala, nu este reala, dar routerul poate livra catre destinatie
  - Solutia 2: `Default Gateway`
    - inainte sa fac antetul de nivel 2, intreb propria statia daca destinatia este in reteaua mea
    - fac AND intre adresa mea si masca si compar cu AND adresa destiantie si masca, 
      - daca este aceeasi retea, fac BROADCAST
      - daca difera reteaua, am nevoie de ajutorul default gateway. O sa ii cer ajutorul 
      - datele se trimit cu adresa MAC a default gateway-ului
- Cate tablee ARP are un router:
  - un router are cate o tabela arp pentru fiecare interfata
- Cate tabele ARP are un SWITCH:
  - NICIUNA!!
  - are entitati de nivel 2, dar nu participa deloc la aceasta convesatie pentru ca este echipament de nivel 3

- exemplu:
  - Daca suntem in aceeasi retea 
    - A1 face broadcast, A2 se recunoaste si trimite direct `UNICAST` la A1
  - Daca suntem in afara retelei
    - default gateway:
      - cer direct default gateway ului, iar acesta o sa imi dea adresa mac corecta
      - dispozitivul meu stie ca nu este in aceeasi retea si isi asuma
    - Proxy arp
      - routerul va minti ca aceea este adresa MAC, senzorul nu stie a cui este MAC-ul, le crede ca este cel corect, dar este cel al routerului
      - senzorul meu are doar 32 biti(doar adresa de IP), nu tine si masca si toate celelalte

