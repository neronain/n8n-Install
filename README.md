# n8n-Install
การติดตั้ง n8n บน Safe-Host พร้อม Postgres

การติดตั้ง Docker และโปรแกรมที่จำเป็นบน Ubuntu 24 หรือ 25 สามารถทำตามขั้นตอนดังนี้:

### ขั้นตอนที่ 1: อัปเดตระบบ

เริ่มต้นด้วยการอัปเดตระบบของคุณให้เป็นเวอร์ชันล่าสุดก่อน:

sudo apt update && sudo apt upgrade -y

### ขั้นตอนที่ 2: ติดตั้ง Dependencies ที่จำเป็น

Docker ต้องการ `apt-transport-https`, `ca-certificates`, `curl`, `software-properties-common`, และ `gnupg` เพื่อทำงานให้ถูกต้อง เราจะติดตั้ง dependencies ดังนี้:

sudo apt install apt-transport-https ca-certificates curl software-properties-common gnupg -y


### ขั้นตอนที่ 3: เพิ่ม Docker GPG Key

เพื่อให้มั่นใจว่าแพ็กเกจ Docker มาจากแหล่งที่เชื่อถือได้ เราจะเพิ่ม GPG key ที่ใช้ในการตรวจสอบแพ็กเกจ Docker:

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

### ขั้นตอนที่ 4: เพิ่ม Repository ของ Docker

ตอนนี้เราจะเพิ่ม repository ของ Docker ลงในระบบ:

echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

### ขั้นตอนที่ 5: ติดตั้ง Docker

ตอนนี้ให้ติดตั้ง Docker ด้วยคำสั่งนี้:

sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io -y

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
docker-compose --version
```

### ขั้นตอนที่ 10: การติดตั้งโปรแกรมที่จำเป็นอื่นๆ (Optional)

หากคุณต้องการติดตั้งโปรแกรมอื่นๆ ที่มักใช้ร่วมกับ Docker เช่น `git`, `curl`, `vim`, คุณสามารถติดตั้งได้ตามต้องการ:

```bash
sudo apt install git curl vim -y
```

ตอนนี้คุณก็จะมี Docker และโปรแกรมที่จำเป็นทั้งหมดติดตั้งเรียบร้อยแล้วบน Ubuntu 24 หรือ 25!

หากต้องการความช่วยเหลือเพิ่มเติมหรือติดปัญหาจุดใด บอกมาได้เลยนะ!
