# Linux Command-line Oefeningen

## 1. Kopieer een groot bestand met `time`
Gebruik het volgende commando om de tijd te meten:

```bash
time cp /path/to/largefile /path/to/destination
```

Herhaal dit commando en vergelijk de twee metingen van tijd.

---

## 2. Schakel process accounting aan
Installeer en activeer de accounting software:

```bash
sudo apt-get install acct  # Installeer de accounting software
sudo service acct start    # Start de service
```

Bekijk de meest gebruikte processen:

```bash
sudo lastcomm
```

---

## 3. Draai `vmstat`
Meet systeemstatistieken met het volgende commando:

```bash
vmstat 2 10
```

Bekijk vooral:
- Kolom `r`: Aantal processen die klaar zijn om te draaien.
- Kolom `b`: Aantal processen die wachten op I/O.

---

## 4. Bekijk de cache die de kernel gebruikt
Gebruik een van de volgende commando's om het geheugen te analyseren:

```bash
free -h
```

of

```bash
cat /proc/meminfo
```

Let op het veld **Cached** in de output.

---

## 5. Controleer IPC-gebruik
Gebruik onderstaand commando om te zien of applicaties IPC (Inter-Process Communication) gebruiken:

```bash
ipcs -a
```

---

## 6. Analyseer `vi` met tools
### a) Bekijk de libraries die `vi` gebruikt:
```bash
ldd $(which vi)
```

### b) Bekijk het geheugengebruik van `vi`:
```bash
pmap $(pgrep vi)
```

### c) Uitleg verschil tussen `ldd` en `pmap`:
- **ldd**: Toont gedeelde bibliotheken gekoppeld tijdens runtime.
- **pmap**: Toont het geheugengebruik van het proces.

---

## 7. Test schijfprestaties met `hdparm`
Meet lees- en schrijfsnelheden van de schijf:

```bash
sudo hdparm -Tt /dev/sda
```

---

## 8. Controleer netwerkprocessen met `lsof`
Toon alle processen die netwerkverbindingen gebruiken:

```bash
sudo lsof -i
```

---

## 9. Maak een netwerkinterface aan en test
### Maak de interface aan:
```bash
sudo ip link add dummy1 type dummy
sudo ip addr add 1.2.3.4/24 dev dummy1
sudo ip link set dev dummy1 up
```

### Test de interface:
```bash
ping 1.2.3.4
```

### Verwijder de interface:
```bash
sudo ip link delete dummy1
```

---

## 10. Oneindige loop in een shell script
Schrijf een shell script met een oneindige loop:

```bash
#!/bin/bash
while true; do
  echo "Running"
done
```

Gebruik `cpulimit` om de CPU-belasting te beperken:

```bash
sudo apt-get install cpulimit
cpulimit -l 10 -p $(pgrep -f "your_script_name")
```

---

## 11. Extra swapfile maken
Maak en activeer een nieuwe swapfile:

```bash
sudo fallocate -l 1G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

Controleer of de swapfile actief is:

```bash
swapon --show
```

---

## 12. Verander een kernelparameter voor swappen
Pas de swapfrequentie aan:

```bash
sudo sysctl vm.swappiness=100
```

Controleer de swapinstellingen met:

```bash
swapon --show
```

---

## 13. Compileer en voer C/C++-bestanden uit
### Compileer de bestanden:
```bash
gcc b.c -o b
g++ c.cpp -o c
```

### Voer de programma's uit:
```bash
./b
./c
```

Als er een fout optreedt, controleer dan de foutmelding. Voor sommige programma's, zoals `b.c`, moet je mogelijk een Apache-server starten:

```bash
sudo service apache2 start
```
