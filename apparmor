# AppArmor Profiel en HTTP Server Configuratie

## Doel
Het doel is om een **globaal AppArmor-profiel** aan te maken voor twee programma's:  
1. **`getstuff.exe`** (Mono-programma).  
2. **`httpserver.py`** (Python-script).

---

## Vereisten
1. **Systeem**: Ubuntu/Debian (zonder grafische interface toegestaan).  
2. **Packages installeren**:
   ```bash
   sudo apt-get update
   sudo apt-get install apparmor-utils auditd mono-complete
   ```

---

## 1. Mono-programma configureren
Volg de **Mono Application Deployment Layout** (REF1):

1. Maak de directory `/usr/lib/getstuff` aan:
   ```bash
   sudo mkdir -p /usr/lib/getstuff
   ```

2. Kopieer `getstuff.exe` naar de nieuwe directory:
   ```bash
   sudo cp getstuff.exe /usr/lib/getstuff/getstuff.exe
   ```

3. Maak een wrapper-script in `/usr/bin` om het programma eenvoudig te starten:
   ```bash
   echo -e '#!/bin/bash\nmono /usr/lib/getstuff/getstuff.exe "$@"' | sudo tee /usr/bin/getstuff
   ```

4. Maak het script uitvoerbaar:
   ```bash
   sudo chmod +x /usr/bin/getstuff
   ```

---

## 2. AppArmor-profiel aanmaken voor `getstuff`

1. Genereer een nieuw profiel met `aa-genprof`:
   ```bash
   sudo aa-genprof /usr/bin/getstuff
   ```

2. Open een **andere terminal** en start `getstuff` als gewone gebruiker:
   ```bash
   cd ~
   getstuff
   ```

3. Terug in de terminal waar `aa-genprof` draait:
   - Kies **Scan** en accepteer de benodigde toegang.
   - Zorg ervoor dat het programma voor alle gebruikers werkt (let op tijdelijke bestanden en procesnummers).

4. Klik op **Finish** om het profiel te activeren in **enforce mode**.

---

## 3. Testen van AppArmor-profiel voor `getstuff`

1. Als root gebruiker:
   ```bash
   sudo su
   cd /root
   getstuff
   ```

2. Controleer of het bestand `httpserver.py` **niet** wordt gedownload:
   ```bash
   ls httpserver.py
   ```

3. Controleer de audit logs:
   ```bash
   sudo less /var/log/audit/audit.log
   ```

---

## 4. Root-toegang toestaan voor downloaden

- Update het AppArmor-profiel voor `getstuff` om root downloads in `/root` toe te staan:
  - Bewerk het bestand: `/etc/apparmor.d/usr.bin.getstuff`.
  - Voeg de benodigde regels toe om `/root` toegang te geven.
  - Laad het profiel opnieuw:
    ```bash
    sudo apparmor_parser -r /etc/apparmor.d/usr.bin.getstuff
    ```

---

## 5. Python-script configureren

Plaats het volgende script in `/usr/lib/httpserver/` als `httpserver.py`:

```python
#!/usr/bin/env python3

from http.server import BaseHTTPRequestHandler, HTTPServer

hostName = "localhost"
hostPort = 8080

# Define the request handler
class RequestHandler(BaseHTTPRequestHandler):
    def do_GET(self):
        # Set the response code and type
        self.send_response(200)
        self.send_header('Content-type', 'text/html')
        self.end_headers()

        # Write the HTML content
        self.wfile.write(b"""
        <!DOCTYPE html>
        <html lang="en">
        <head>
            <meta charset="UTF-8">
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <title>Server is Working</title>
        </head>
        <body>
            <h1>Server is Working!</h1>
            <p>The server is up and running. This is a basic HTML page served by a Python HTTP server.</p>
        </body>
        </html>
        """)

# Set up the server to listen on port 8080 (non-privileged port)
def run(server_class=HTTPServer, handler_class=RequestHandler, port=hostPort):
    server_address = ('', port)
    httpd = server_class(server_address, handler_class)
    print(f"Server running on port {port}...")
    httpd.serve_forever()

# Run the server
if __name__ == '__main__':
    run()
```

Maak het uitvoerbaar:
```bash
sudo chmod +x /usr/lib/httpserver/httpserver.py
```

---

## 6. AppArmor-profiel maken voor `httpserver`

1. Genereer een nieuw profiel met `aa-genprof`:
   ```bash
   sudo aa-genprof /usr/bin/httpserver
   ```

2. Start `httpserver` en test via je browser:
   - Start het script:
     ```bash
     httpserver
     ```
   - Open de browser en ga naar: [http://localhost:8080](http://localhost:8080).

3. Configureer het profiel zoals eerder en test de toegang.

---

## Herstart of opnieuw maken van profielen

- Verwijder bestaande profielen:
  ```bash
  sudo rm /etc/apparmor.d/usr.bin.getstuff /etc/apparmor.d/usr.bin.httpserver
  ```

- Herstart het systeem om opnieuw te beginnen:
  ```bash
  sudo reboot
  ```

---

Met dit plan kun je de gevraagde AppArmor-profielen configureren en de beveiliging testen. Als je ergens vastloopt, laat het weten!
