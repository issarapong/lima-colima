# ตัวอย่างคำสั่ง Lima CLI

## การติดตั้ง Lima

```bash
# ติดตั้งผ่าน Homebrew (macOS)
brew install lima

# ติดตั้งผ่าน Homebrew (Linux)
brew install lima

# หรือดาวน์โหลด binary โดยตรง
curl -fsSL https://github.com/lima-vm/lima/releases/latest/download/lima-$(uname -s)-$(uname -m).tar.gz | tar Cxzvm /usr/local
```

## การจัดการ VM

### สร้างและเริ่ม VM

```bash
# สร้าง VM ด้วยค่า default
limactl start

# สร้าง VM พร้อมตั้งชื่อ
limactl start my-ubuntu

# สร้าง VM จาก template ที่กำหนด
limactl start --name=docker template://docker

# สร้าง VM จาก YAML config file
limactl start --name=custom ./my-config.yaml

# สร้าง VM พร้อมปรับแต่ง CPU และ Memory
limactl start --cpus=4 --memory=8
```

### รายการและตรวจสอบ VM

```bash
# แสดงรายการ VM ทั้งหมด
limactl list

# แสดงข้อมูลละเอียดของ VM
limactl show my-ubuntu

# ตรวจสอบสถานะ VM
limactl status my-ubuntu
```

### การเข้าใช้งาน VM

```bash
# เข้าใช้งาน VM default
lima

# เข้าใช้งาน VM ที่ระบุชื่อ
lima my-ubuntu

# รันคำสั่งเดียวใน VM
lima uname -a

# รันคำสั่งเดียวใน VM ที่ระบุชื่อ
lima my-ubuntu docker ps

# เปิด shell แบบ root
limactl shell --workdir=/ my-ubuntu sudo -i
```

### การหยุดและลบ VM

```bash
# หยุด VM
limactl stop my-ubuntu

# หยุด VM ทั้งหมด
limactl stop --all

# เริ่ม VM ใหม่
limactl start my-ubuntu

# ลบ VM (จะหยุด VM ก่อนถ้ายังทำงานอยู่)
limactl delete my-ubuntu

# ลบ VM แบบบังคับ (ไม่ถามยืนยัน)
limactl delete --force my-ubuntu
```

## การจัดการ Files และ Directories

```bash
# ไฟล์ใน home directory ของ host จะ mount อัตโนมัติใน /Users/<username> (macOS)
# หรือ /home/<username> (Linux)

# คัดลอกไฟล์จาก host ไปยัง VM
lima cp local-file.txt remote-path/

# คัดลอกไฟล์จาก VM มาที่ host
lima cp remote-path/file.txt ./local-path/

# เข้าถึงไฟล์ของ host ใน VM
lima ls /Users/$USER/Documents
```

## การทำงานกับ Containers

```bash
# ใช้ nerdctl (containerd CLI) ใน VM
lima nerdctl run -d -p 8080:80 nginx

# ดู containers ที่กำลังทำงาน
lima nerdctl ps

# ดู images
lima nerdctl images

# Build image
lima nerdctl build -t my-app:latest .

# Run container แบบ interactive
lima nerdctl run -it ubuntu bash
```

## การตั้งค่า Environment Variables

```bash
# ตั้งค่า environment variable เมื่อรันคำสั่ง
lima env VAR=value command

# แก้ไข environment variables ใน VM
limactl edit my-ubuntu
# จากนั้นเพิ่มใน section "env"
```

## การทำ Port Forwarding

```bash
# Port forwarding ทำงานอัตโนมัติสำหรับ ports ที่ bind ใน VM
# ตัวอย่าง: รัน web server บน port 8080 ใน VM
lima nerdctl run -d -p 8080:80 nginx

# จากนั้นเข้าถึงได้จาก host: http://localhost:8080
```

## การแก้ไขการตั้งค่า VM

```bash
# แก้ไข config ของ VM ที่กำลังทำงาน
limactl edit my-ubuntu

# แก้ไขแล้วต้อง restart VM เพื่อให้การเปลี่ยนแปลงมีผล
limactl stop my-ubuntu
limactl start my-ubuntu
```

## การ Debug และ Troubleshooting

```bash
# ดู logs ของ VM
limactl show-log my-ubuntu

# ตรวจสอบ IP address ของ VM
limactl show my-ubuntu | grep "IP Address"

# เช็คการใช้งานทรัพยากร
lima top

# เข้าถึง VM ผ่าน SSH โดยตรง
limactl shell my-ubuntu
```

## Templates ที่มีให้ใช้งาน

```bash
# แสดงรายการ templates ที่มี
limactl start --list-templates

# ใช้ template สำหรับ Docker
limactl start --name=docker-vm template://docker

# ใช้ template สำหรับ Kubernetes
limactl start --name=k8s-vm template://k8s

# ใช้ template สำหรับ Ubuntu
limactl start --name=ubuntu-vm template://ubuntu

# ใช้ template สำหรับ Fedora
limactl start --name=fedora-vm template://fedora
```

## การทำ Snapshot (บาง versions)

```bash
# บาง VM drivers รองรับ snapshot
# ตรวจสอบว่า driver ของคุณรองรับหรือไม่

# สร้าง snapshot
limactl snapshot create my-ubuntu snap1

# แสดงรายการ snapshots
limactl snapshot list my-ubuntu

# Restore จาก snapshot
limactl snapshot restore my-ubuntu snap1

# ลบ snapshot
limactl snapshot delete my-ubuntu snap1
```

## Tips และ Best Practices

```bash
# ใช้ alias เพื่อความสะดวก
echo "alias lm='lima'" >> ~/.bashrc
echo "alias lctl='limactl'" >> ~/.bashrc

# ตั้งค่า default instance
export LIMA_INSTANCE=my-ubuntu

# ตรวจสอบ version
limactl --version

# ดู help
limactl --help
limactl start --help
```

## การอัพเกรด Lima

```bash
# อัพเกรดผ่าน Homebrew
brew upgrade lima

# ตรวจสอบ version หลังอัพเกรด
limactl --version

# VM เดิมจะยังทำงานได้ตามปกติหลังอัพเกรด
```
