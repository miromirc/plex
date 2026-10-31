# Plex Media Server z Dockerjem na Ubuntu + Omrežni priklopi (SMB)

Ta repozitorij vsebuje konfiguracijo in vodnik za postavitev lastnega **Plex Media Serverja** v Docker vsebniku na Ubuntu strežniku, vključno z integracijo lokalnih medijskih map in omrežnih pogonov (SMB/CIFS) iz drugih računalnikov (npr. Windows).

---

## 🛠️ 1. Namestitev in zagon Plex strežnika v Dockerju

### 1. Priprava mape
Na svojem Ubuntu strežniku ustvari mapo za projekt in se pomakni vanjo:
```bash
mkdir -p ~/plex-docker && cd ~/plex-docker
```

### 2. Konfiguracija `docker-compose.yml`
Ustvari datoteko z urejevalnikom besedil:
```bash
nano docker-compose.yml
```

Vanjo prilepi spodnjo vsebino in jo prilagodi svojim potem:
```yaml
services:
  plex:
    image: plexinc/pms-docker:latest
    container_name: plex
    restart: unless-stopped
    network_mode: host
    environment:
      - TZ=Europe/Ljubljana
      - PLEX_CLAIM=****   # <--- Zamenjaj s svojim claim žetonom s plex.tv/claim
    volumes:
      - ./config:/config
      - /mnt/storage/Downloads/Filmi:/data/filmi
      - /mnt/storage/Downloads/Risanke:/data/risanke
      - /mnt/risanke:/data/omrezne_risanke
```
*(Shrani z `Ctrl + O`, potrdi z `Enter` in zapri z `Ctrl + X`).*

### 3. Zagon vsebnika
Zaženi Docker vsebnik v ozadju:
```bash
docker compose up -d
```

Preveri delovanje:
```bash
docker ps
```

---

## 🌐 2. Priklop omrežne mape z drugega računalnika (Windows -> Ubuntu)

Če želiš v Ubuntu priklopiti omrežni pogon ali mapo iz drugega računalnika (brez gesla oziroma kot *guest*):

### 1. Namestitev SMB orodij
Na Ubuntu strežniku namesti paket za deljenje datotek:
```bash
sudo apt update && sudo apt install cifs-utils -y
```

### 2. Ustvarjanje lokalne točke za priklop
```bash
sudo mkdir -p /mnt/risanke
```

### 3. Priklop omrežne poti
Poveži omrežni vir v lokalno mapo:
```bash
sudo mount -t cifs -o guest **** /mnt/risanke
```
*(Kjer so `****` omrežna pot do tvoje mape, npr. `//IP_RACUNALNIKA/ImeMape`).*

Preveri uspešnost priklopa:
```bash
ls /mnt/risanke
```

---

## 🔑 Razlaga spremenljivk (`****`)

* **`PLEX_CLAIM=****`**: Tvoj edinstveni žeton, ki ga pridobiš na [plex.tv/claim](https://www.plex.tv/claim/), da se strežnik poveže s tvojim Plex računom.
* **`****` v ukazu za mount**: Omrežno ime/pot do deljene mape na drugem računalniku (npr. `//192.168.1.50/Risanke` ali `//IME_RACUNALNIKA/ImeMape`).