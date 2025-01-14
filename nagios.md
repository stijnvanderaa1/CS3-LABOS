# 💙🤍W6P1 Nagios Installatie & Configuratie Gids💙🤍

## 📝 Voorbereiding

**Doel**: Installeren en configureren van Nagios en Nagios-plugins.  
**Benodigdheden**:
- **Controller (server01)**: Ubuntu 24.04, 10GB opslag, 3GB RAM, netwerk: NAT + Host-Only.
- **Managed node (server02)**: Ubuntu 24.04, 10GB opslag, 512MB RAM, netwerk: NAT + Host-Only.

---

## ✨ Exercises

### 👉 Exercise 0: Installeren van benodigde pakketten

Op beide servers:

```bash
sudo apt-get update -y && sudo apt-get upgrade -y
sudo timedatectl set-timezone Europe/Brussels
```

**Server01**:  
Installeer Nagios en vereiste pakketten:

```bash
sudo apt install nagios4 nagios-nrpe-plugin openssh-server nagstamon -y
```

Kies bij installatie:
- **Mailserver**: "No configuration"
- **Nagiosadmin wachtwoord**: Kies een wachtwoord.

Test of de webinterface bereikbaar is:

```bash
curl http://localhost/nagios4/
```

**Server02**:  
Installeer de agent:

```bash
sudo apt install nagios-nrpe-server openssh-server -y
```

---

### 👉 Exercise 1: Basisconfiguratie

**Server01**:

**Netwerk instellen**:

IP: 10.1.0.254.  
Voeg de volgende configuratie toe aan `/etc/netplan/01-netcfg.yaml`:

```yaml
network:
  version: 2
  ethernets:
    ens33:
      dhcp4: no
      dhcp6: no
      addresses:
        - 10.1.0.254/24
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
```

Toepassen:

```bash
sudo netplan apply
```

**Hostnamen instellen**:

```bash
sudo hostnamectl set-hostname server01
echo -e "127.0.0.1 localhost\n10.1.0.254 server01\n10.1.0.1 server02" | sudo tee /etc/hosts
```

**Apache modules en Nagios configureren**:

```bash
sudo a2enmod cgi
sudo a2enconf nagios4-cgi
sudo systemctl restart apache2
```

**Authenticatie configureren**:

Maak een gebruiker `nagiosadmin` aan:

```bash
sudo htdigest -c /etc/nagios4/htdigest.users nagiosadmin nagiosadmin
```

Schakel authenticatie in via `/etc/nagios4/cgi.cfg`:

```bash
sudo sed -i 's/^use_authentication=0/use_authentication=1/' /etc/nagios4/cgi.cfg
sudo sed -i 's/^#default_user_name=guest/default_user_name=nagiosadmin/' /etc/nagios4/cgi.cfg
```

---

### 👉 Exercise 2: Server01 Configureren

**Host configureren**:

Kopieer `localhost.cfg` naar `server02.cfg`:

```bash
sudo cp /etc/nagios4/objects/localhost.cfg /etc/nagios4/conf.d/server02.cfg
```

Voeg de volgende definitie toe aan `server02.cfg`:

```dns
define host {
    use                      generic-host
    host_name                server02
    address                  192.168.59.103
    max_check_attempts       5
    check_interval           5
    retry_interval           1
}
```

Zet hostgroepen in commentaar (`#`).

**Services activeren**:

Schakel alle services in `server02.cfg` in.  
Nagios opnieuw opstarten:

```bash
sudo nagios4 -v /etc/nagios4/nagios.cfg
sudo systemctl restart nagios4
```

---

### 👉 Exercise 3: Server02 Configureren

**NRPE configureren**:

Open `/etc/nagios/nrpe.cfg` en:
- Voeg server01 (10.1.0.254) toe aan `allowed_hosts`.
- Schakel alle checks in.
- Pas de harddisk-check aan naar `check_all_disks`.

**NRPE opnieuw opstarten**:

```bash
sudo systemctl restart nagios-nrpe-server
```

**Status controleren**:

Test of server02 gegevens zichtbaar zijn via de webinterface van server01:

```bash
curl http://localhost/nagios4/
```

---

### 👉 Exercise 4: Eigen Plugin Schrijven

**Maak een Python-plugin**:

Maak een bestand `/usr/lib/nagios/plugins/check_services.py`:

```python
#!/usr/bin/env python3
import os
import sys

http = os.system("systemctl is-active apache2 > /dev/null")
ssh = os.system("systemctl is-active ssh > /dev/null")

if http == 0 and ssh == 0:
    print("OK - HTTP and SSH are running")
    sys.exit(0)
elif ssh != 0 and http == 0:
    print("WARNING - SSH is not running, HTTP is running")
    sys.exit(1)
else:
    print("CRITICAL - Both HTTP and SSH are not running")
    sys.exit(2)
```

Maak het script uitvoerbaar:

```bash
sudo chmod +x /usr/lib/nagios/plugins/check_services.py
```

Voeg de plugin toe aan `nrpe.cfg`:

```bash
echo "command[check_services]=/usr/lib/nagios/plugins/check_services.py" | sudo tee -a /etc/nagios/nrpe.cfg
```

**Herstart NRPE**:

```bash
sudo systemctl restart nagios-nrpe-server
```

Controleer de plugin in de webinterface.
