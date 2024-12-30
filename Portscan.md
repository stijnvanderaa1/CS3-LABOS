# Portscan
------------------------------------

1) **Hoe werd het binaire logbestand portscan2021.pcap aangemaakt?**
    - _sudo tcpdump -i eth0 -w portscan2021.pcap_
    - in wireshark: opnemen verkeer


2) Wat is het IP adres van de aanvaller?
    - 192.168.56.1: doormiddel van in wireshark dit commando: _tcp.flags.syn == 1 && tcp.flags.ack == 0_ (hier het source adress)
    
      -> portscan


3) Wat is het IP adres van de target?
    - 192.168.56.103: het destination adress


3) Welke poorten staan open op de target?
    - _tcp.flags.syn == 1 && tcp.flags.ack == 1_ : elke poort die een SYN/ACK terugstuurt is een open TCP poort


3) De target werd op minstens 4 manieren gescand. Geef de 4 methodes en leg uit hoe ze werken.
    - **Port sweep (ACK-scan)**
      -> Een port sweep is een scan die alle poorten op een target scant.
    
    - **Stealth-scan**
      -> Als het target reageert met een SYN/ACK-pakket, is de poort open. Als het target reageert met een RST-pakket, is de poort gesloten.
    
    - **XMAS-scan**
      -> Een XMAS-scan is een scan die een pakket verstuurt met de FIN-, URG- en PSH-vlaggen ingesteld op 1. Als het target reageert met een RST-pakket, is de poort gesloten. Als het target niet reageert, is de poort open.
    
    - **FIN-scan**
      -> Een FIN-scan is een scan die een pakket verstuurt met de FIN-vlag ingesteld op 1. Als het target reageert met een RST-pakket, is de poort gesloten. Als het target niet reageert, is de poort open.

3) Welke scantool werd gebruikt om de target te scannen? Hoe weet je dit?
    - doordat er veel _FYN_, _XMAS_ en _Port_ scans worden gebruikt gaan we ervan uit dat het **NMAP** is.


3) Welke commando's heeft de aanvaller gebruikt? (geef deze zo volledig mogelijk met alle parameters)
    - _nmap -sS -sV -O -T4 -p- 192.168.1.1 -oN scan.txt_
        -> Dit Nmap-commando wordt gebruikt om een stealthy SYN-scan uit te voeren op alle poorten van het doelwit 192.168.1.1. Het probeert ook de versies van de services op de open poorten te identificeren en het besturingssysteem van het doelwit te detecteren. De scan wordt uitgevoerd met een redelijk agressieve timing (-T4) om de scan sneller te maken, en de resultaten worden opgeslagen in een tekstbestand (scan.txt).


3) Welk besturingssysteem draait de aanvaller?
    - _Linux_
      
        -> Windows: TTL-waarde van 128.
      
        -> Linux: TTL-waarde van 64.
      
        -> Mac OS: TTL-waarde van 64 of 255.


3) Stel snort in (zorg dat portscans gedetecteerd worden) en laat snort de portscan analyseren.
Welke aanval(len) zie je in de logs?
    - k
  
