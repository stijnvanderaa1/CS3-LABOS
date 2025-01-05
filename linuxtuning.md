1. Kopieer een groot bestand met "time"
bash
```
time cp /path/to/largefile /path/to/destination
```
Herhaal dit nog een keer en vergelijk de twee metingen van tijd.

2. Schakel process accounting aan
bash
```
sudo apt-get install acct  # Installeer de accounting software
sudo service acct start    # Start de service
```
# Bekijk de meest gebruikte processen
```
sudo lastcomm
```
3. Draai vmstat
bash
```
vmstat 2 10
```
Hiermee wordt om de 2 seconden 10 metingen weergegeven. Bekijk het aantal processen in de rij "r" (voor processen die klaar zijn om te draaien) en "b" (voor processen die wachten op I/O).

4. Bekijk de cache die de kernel gebruikt
bash
```
free -h
```
Of

bash
```
cat /proc/meminfo
```
Kijk naar het veld Cached in de output. Dit geeft informatie over het gebruik van de pagina cache.

5. Draai IPC-gebruikende applicaties
Gebruik bijvoorbeeld de volgende commando's om te zien of applicaties IPC gebruiken:

bash
```
ipcs -a
```
6. Vi editor
a) Gebruik ldd om te zien welke libraries vi gebruikt:

bash
```
ldd $(which vi)
```
b) Gebruik pmap om het geheugengebruik van vi te bekijken:

bash
```
pmap $(pgrep vi)  # Gebruik pgrep om het PID van vi te krijgen
```
c) Verschil tussen de bibliotheken die pmap en ldd tonen:

ldd toont de gedeelde bibliotheken die tijdens runtime gekoppeld worden.
pmap toont het geheugengebruik van het proces en de virtuele geheugenstructuren.
7. Test de prestaties van je schijf met hdparm
bash
```
sudo hdparm -Tt /dev/sda
```
Dit geeft informatie over de lees- en schrijfsnelheden van de schijf.

8. Gebruik lsof om netwerkprocessen te controleren
bash
```
sudo lsof -i
```
Dit toont alle processen die netwerkverbindingen gebruiken.

10. Maak een netwerkinterface aan en test met ip
bash
```
sudo ip link add dummy1 type dummy
sudo ip addr add 1.2.3.4/24 dev dummy1
sudo ip link set dev dummy1 up
```
# Test of je kunt pingen
```
ping 1.2.3.4
```

# Verwijder de interface
```
sudo ip link delete dummy1
```
10. Oneindige loop in shell script
Schrijf een shell script met een oneindige loop:

bash
```
#!/bin/bash
while true; do
  echo "Running"
done
```
Gebruik cpulimit om de CPU-belasting te beperken:

bash
```
sudo apt-get install cpulimit
cpulimit -l 10 -p $(pgrep -f "your_script_name")
```
Dit zorgt ervoor dat de loop weinig CPU gebruikt.

11. Extra swapfile maken
Maak een swapfile:

bash
```
sudo fallocate -l 1G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```
# Controleer of het systeem de swapfile gebruikt
```
swapon --show
```
12. Verander een kernelparameter voor swappen
Wijzig de swapfrequentie in de kernel:

bash
```
sudo sysctl vm.swappiness=100
```
Dit zorgt ervoor dat het systeem sneller naar swap zal gaan. Controleer de swap met swapon --show.

13. Compileer en voer c/c++-bestanden uit
Om te compileren:

bash
```
gcc b.c -o b
g++ c.cpp -o c
```
Start de programma's:

bash
```
./b
./c
```
Als er een fout optreedt, lees dan de foutmelding en los deze op. Voor b.c moet je wellicht een Apache-server draaien:

bash
```
sudo service apache2 start
```
Deze opdrachten zouden je moeten helpen de oefeningen uit te voeren!
