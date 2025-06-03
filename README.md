# n8n-Install
การติดตั้ง n8n บน Safe-Host พร้อม Postgres

การติดตั้ง Docker และโปรแกรมที่จำเป็นบน Ubuntu 24 หรือ 25 สามารถทำตามขั้นตอนดังนี้:

### ขั้นตอนที่ 1: อัปเดตระบบ

เริ่มต้นด้วยการอัปเดตระบบของคุณให้เป็นเวอร์ชันล่าสุดก่อน:
```bash
sudo apt update && sudo apt upgrade -y
```
### ขั้นตอนที่ 2: ติดตั้ง Dependencies ที่จำเป็น

Docker ต้องการ `apt-transport-https`, `ca-certificates`, `curl`, `software-properties-common`, และ `gnupg` เพื่อทำงานให้ถูกต้อง เราจะติดตั้ง dependencies ดังนี้:
```bash
sudo apt install apt-transport-https ca-certificates curl software-properties-common gnupg -y
```

### ขั้นตอนที่ 3: เพิ่ม Docker GPG Key

เพื่อให้มั่นใจว่าแพ็กเกจ Docker มาจากแหล่งที่เชื่อถือได้ เราจะเพิ่ม GPG key ที่ใช้ในการตรวจสอบแพ็กเกจ Docker:
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```
### ขั้นตอนที่ 4: เพิ่ม Repository ของ Docker

ตอนนี้เราจะเพิ่ม repository ของ Docker ลงในระบบ:
```bash
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
### ขั้นตอนที่ 5: ติดตั้ง Docker

ตอนนี้ให้ติดตั้ง Docker ด้วยคำสั่งนี้:
```bash
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin-y
```
### ขั้นตอนที่ 6: ตรวจสอบการติดตั้ง Docker

หลังจากติดตั้งเสร็จแล้ว ให้ตรวจสอบว่า Docker ทำงานได้หรือไม่:

```bash
sudo systemctl status docker
```

หาก Docker ทำงานได้ถูกต้องจะเห็นสถานะ "active (running)" ปรากฏขึ้น

### ขั้นตอนที่ 7: การตั้งค่า Docker ให้รันโดยไม่ต้องใช้ `sudo`

ถ้าคุณต้องการให้ Docker รันได้โดยไม่ต้องใช้คำสั่ง `sudo` ทุกครั้ง (โดยการเพิ่มผู้ใช้ในกลุ่ม `docker`):

```bash
sudo usermod -aG docker $USER
```

จากนั้นให้ออกจากระบบแล้วกลับเข้ามาใหม่ หรือใช้คำสั่งนี้เพื่อให้การเปลี่ยนแปลงมีผล:

```bash
newgrp docker
```

### ขั้นตอนที่ 8: ทดสอบการติดตั้ง Docker

ทดสอบการทำงานของ Docker ด้วยการรันคำสั่ง `docker` เพื่อดึง image ตัวอย่างและให้ Docker ทำงาน:

```bash
docker run hello-world
```

### ขั้นตอนที่ 9: ติดตั้ง Docker Compose

หากคุณต้องการใช้ Docker Compose ซึ่งเป็นเครื่องมือสำหรับการจัดการ container หลายๆ ตัวพร้อมกัน สามารถติดตั้งได้ตามขั้นตอนนี้:

1. ดาวน์โหลด Docker Compose:

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

2. เปลี่ยนสิทธิ์ให้ไฟล์สามารถรันได้:

```bash
sudo chmod +x /usr/local/bin/docker-compose
```

3. ตรวจสอบเวอร์ชันของ Docker Compose:

```bash
docker --version && docker-compose --version && netstat --version && nano --version
```

### ขั้นตอนที่ 10: การติดตั้งโปรแกรมที่จำเป็นอื่นๆ (Optional)

หากคุณต้องการติดตั้งโปรแกรมอื่นๆ ที่มักใช้ร่วมกับ Docker เช่น `git`, `curl`, `vim`, คุณสามารถติดตั้งได้ตามต้องการ:

```bash
sudo apt install git curl vim -y
```

การตั้งค่า **n8n** (เครื่องมือทำงานอัตโนมัติแบบโอเพนซอร์ส) ด้วย **Docker Compose** บน Ubuntu มีขั้นตอนดังนี้:

### ขั้นตอนที่ 1: สร้างโฟลเดอร์สำหรับ n8n

เริ่มต้นด้วยการสร้างโฟลเดอร์ที่จะเก็บไฟล์ `docker-compose.yml` และการตั้งค่าของ n8n:

```bash
mkdir ~/n8n-docker
cd ~/n8n-docker
```

### ขั้นตอนที่ 2: สร้างไฟล์ `docker-compose.yml`

สร้างไฟล์ `docker-compose.yml` ในโฟลเดอร์ที่สร้างขึ้นมา:

```bash
nano docker-compose.yml
```

จากนั้นให้เพิ่มโค้ดนี้ลงไปในไฟล์:

```yaml
version: '3'

services:
  n8n:
    image: docker.n8n.io/n8nio/n8n
    container_name: n8n
    environment:
      - N8N_HOST=https://localhost
      - N8N_EDITOR_BASE_URL=https://localhost
      - WEBHOOK_URL=https://localhost
      - GENERIC_TIMEZONE=Asia/Bangkok
      - TZ=Asia/Bangkok
      - DB_TYPE=postgresdb
      - DB_POSTGRESDB_DATABASE=n8n
      - DB_POSTGRESDB_HOST=postgresql
      - DB_POSTGRESDB_PORT=5432
      - DB_POSTGRESDB_USER=n8n_user
      - DB_POSTGRESDB_SCHEMA=public
      - DB_POSTGRESDB_PASSWORD=n8npassword
    ports:
      - 5678:5678
    volumes:
      - n8n-data:/home/node/.n8n
    depends_on:
      - postgresql
    restart: always

  n8n-db:
    image: postgres:16-alpine
    container_name: postgresql
    environment:
      - POSTGRES_DB=n8n
      - POSTGRES_USER=n8n_user
      - POSTGRES_PASSWORD=n8npassword
    volumes:
      - postgresql-data:/var/lib/postgresql/data
    restart: always

volumes:
  n8n-data:
  postgresql-data:
```

### คำอธิบาย:

* **n8n**: จะใช้ภาพ `n8n/n8n` ล่าสุดจาก Docker Hub และรัน n8n ที่พอร์ต 5678
* **n8n-db**: ใช้ PostgreSQL ในการเก็บข้อมูล โดยใช้ตัวแปรที่กำหนด เช่น `POSTGRES_DB`, `POSTGRES_USER`, และ `POSTGRES_PASSWORD`
* **Basic Auth**: ตั้งค่าการใช้งาน n8n โดยใช้การยืนยันตัวตนแบบเบื้องต้น (`N8N_BASIC_AUTH_ACTIVE=true`) และกำหนดชื่อผู้ใช้และรหัสผ่าน

### ขั้นตอนที่ 3: เริ่มต้น Docker Compose

หลังจากสร้างไฟล์ `docker-compose.yml` แล้ว ให้เริ่มต้นการใช้งาน Docker Compose ด้วยคำสั่ง:

```bash
docker-compose up -d
```

คำสั่งนี้จะดาวน์โหลดภาพ Docker ที่จำเป็นและเริ่มต้นการใช้งาน n8n และฐานข้อมูล PostgreSQL ในโหมดเบื้องหลัง

### ขั้นตอนที่ 4: ตรวจสอบสถานะ

เพื่อให้แน่ใจว่า n8n กำลังทำงานอยู่, คุณสามารถตรวจสอบสถานะของ container ด้วยคำสั่ง:

```bash
docker-compose ps
```

ถ้า container ทั้งสอง (`n8n` และ `n8n-db`) รันอยู่ คุณจะเห็นสถานะที่เป็น **Up**.

### ขั้นตอนที่ 5: เข้าถึง n8n

ตอนนี้คุณสามารถเข้าใช้งาน n8n ได้โดยเปิดเว็บบราวเซอร์และไปที่ URL ต่อไปนี้:

```
http://localhost:5678
```

จากนั้นให้ใส่ **ชื่อผู้ใช้** และ **รหัสผ่าน** ที่คุณตั้งไว้ในไฟล์ `docker-compose.yml` (ในตัวอย่างนี้คือ `admin` และ `adminpassword`).

### ขั้นตอนที่ 6: ปรับการตั้งค่าพอร์ต (ถ้าจำเป็น)

ถ้าคุณต้องการเปลี่ยนพอร์ตในการเข้าถึง n8n คุณสามารถแก้ไขพอร์ตในไฟล์ `docker-compose.yml` ในส่วนของ `ports` ดังนี้:

```yaml
ports:
  - 8080:5678
```

ในตัวอย่างนี้ `8080` จะเป็นพอร์ตใหม่ที่คุณสามารถใช้ในการเข้าถึง n8n.

### ขั้นตอนที่ 7: การหยุดและเริ่มใหม่

หากคุณต้องการหยุดการทำงานของ n8n:

```bash
docker-compose down
```

และหากคุณต้องการเริ่มใหม่:

```bash
docker-compose up -d
```


### ขั้นตอนที่ 8: ตั้งค่า SSL ด้วย Certbot
```bash
sudo mkdir -p /etc/nginx/ssl/n8n

#สร้าง self-signed certificate
sudo openssl req -x509 -nodes -day 365 -newkey rsa:2048 \
  -keyout /etc/nginx/ssl/n8n/server.key \
  -out /etc/nginx/ssl/n8n/server.crt \
  -subj "/C=TH/ST=Bangkok/L=Bangkok/O=n8n-setup/CN=YOUR_VM_IP" \
  -addext "subjectAltName = IP:YOUR_VM_IP"
```

Update Config Nginx เพื่อใช้งาน SSL
```bash
sudo nano /etc/nginx/sites-available/n8n
```

```bash
server {
  listen 80;
  server_name YOUR_VM_IP;
  return 301 https://$host$request_uri;
}
server {
  listen 443 ssl;
  server_name YOUR_VM_IP;

  ssl_certificate /etc/nginx/ssl/n8n/server.crt;
  ssl_certificate_key /etc/nginx/ssl/n8n/server.key;
  ssl_protocols TLSv1.2 TLSv1.3;
  ssl_ciphers HIGH:!aNULL:!MD5;
  ssl_prefer_server_ciphers on;

  ssl_session_cache shared:SSL:10m;
  ssl_session_timeout 10m;

  client_max_body_size 100M;

location / {
  proxy_pass http://localhost:5678;
  proxy_http_version 1.1;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection “upgrade”;
  proxy_set_header Host $host;
  proxy_set_header X-Real-IP $remote_addr;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header X-Forwarded-Proto $scheme;
  }

}
```

```bash
sudo ln -s /etc/nginx/sites-available/n8n /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```
