# ตัวอย่างคำสั่ง Colima CLI

## การติดตั้ง Colima

```bash
# ติดตั้งผ่าน Homebrew (macOS)
brew install colima

# ติดตั้งผ่าน Homebrew (Linux)
brew install colima

# ติดตั้งผ่าน MacPorts (macOS)
sudo port install colima

# หรือติดตั้งจาก binary โดยตรง
curl -LO https://github.com/abiosoft/colima/releases/latest/download/colima-$(uname)-$(uname -m)
sudo install colima-$(uname)-$(uname -m) /usr/local/bin/colima
```

## การเริ่มต้นและจัดการ Colima

### เริ่มต้น Colima

```bash
# เริ่มต้นด้วยค่า default (2 CPUs, 2GB RAM, 60GB disk)
colima start

# เริ่มต้นพร้อมกำหนด CPU และ Memory
colima start --cpu 4 --memory 8

# เริ่มต้นพร้อมกำหนดขนาด disk (GiB)
colima start --disk 100

# เริ่มต้นพร้อมกำหนด architecture
colima start --arch aarch64  # สำหรับ ARM64
colima start --arch x86_64   # สำหรับ Intel/AMD

# เริ่มต้นพร้อมกำหนด VM type
colima start --vm-type vz      # Virtualization.framework (macOS 13+)
colima start --vm-type qemu    # QEMU (default)

# เริ่มต้นพร้อม Kubernetes
colima start --kubernetes

# เริ่มต้นพร้อม Kubernetes version ที่กำหนด
colima start --kubernetes --kubernetes-version v1.28.0

# เริ่มต้นพร้อมเลือก runtime (docker หรือ containerd)
colima start --runtime docker
colima start --runtime containerd

# เริ่มต้นพร้อม DNS resolver
colima start --dns 8.8.8.8 --dns 8.8.4.4
```

### การตรวจสอบสถานะ

```bash
# ตรวจสอบสถานะ Colima
colima status

# แสดงข้อมูลละเอียด
colima status --verbose

# แสดง version
colima version
```

### รายการและจัดการ instances

```bash
# แสดงรายการ instances ทั้งหมด
colima list

# เริ่มต้น instance ที่มีชื่อกำหนด (profile)
colima start --profile development
colima start --profile testing

# ตรวจสอบสถานะของ profile ที่กำหนด
colima status --profile development

# สลับระหว่าง profiles
colima stop --profile development
colima start --profile testing
```

### การหยุดและลบ

```bash
# หยุด Colima
colima stop

# หยุด Colima profile ที่กำหนด
colima stop --profile development

# ลบ Colima (ลบ VM และข้อมูลทั้งหมด)
colima delete

# ลบแบบบังคับ (ไม่ถามยืนยัน)
colima delete --force

# ลบ profile ที่กำหนด
colima delete --profile development
```

## การทำงานกับ Docker

### คำสั่ง Docker พื้นฐาน

```bash
# ตรวจสอบว่า Docker ทำงาน
docker version
docker info

# รัน container
docker run hello-world
docker run -d --name nginx -p 8080:80 nginx

# ดู containers ที่กำลังทำงาน
docker ps

# ดู containers ทั้งหมด (รวมที่หยุดแล้ว)
docker ps -a

# ดู images
docker images

# ดู volumes
docker volume ls

# ดู networks
docker network ls
```

### Build และ Run Applications

```bash
# Build Docker image
docker build -t my-app:latest .

# Run container จาก image ที่ build
docker run -d -p 3000:3000 my-app:latest

# Run พร้อม environment variables
docker run -d -e NODE_ENV=production -p 3000:3000 my-app:latest

# Run พร้อม volume mount
docker run -d -v $(pwd):/app -p 3000:3000 my-app:latest

# Run แบบ interactive
docker run -it ubuntu bash

# เข้าไปใน running container
docker exec -it <container-id> bash
```

### Docker Compose

```bash
# รัน services ด้วย Docker Compose
docker compose up

# รันใน background
docker compose up -d

# หยุด services
docker compose down

# หยุดและลบ volumes
docker compose down -v

# ดู logs
docker compose logs
docker compose logs -f  # follow logs

# Build และ run
docker compose up --build
```

## การทำงานกับ Kubernetes

### เริ่มต้น Kubernetes

```bash
# เริ่ม Colima พร้อม Kubernetes
colima start --kubernetes

# เริ่มพร้อมกำหนด version และ resources
colima start --kubernetes \
  --kubernetes-version v1.28.0 \
  --cpu 4 \
  --memory 8 \
  --disk 100
```

### คำสั่ง kubectl พื้นฐาน

```bash
# ตรวจสอบว่า kubectl เชื่อมต่อกับ cluster
kubectl cluster-info
kubectl get nodes

# ดู context
kubectl config get-contexts
kubectl config current-context

# เปลี่ยน context (ถ้ามีหลาย clusters)
kubectl config use-context colima

# Deploy application
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --port=80 --type=NodePort

# ดู pods
kubectl get pods
kubectl get pods -A  # ทุก namespaces

# ดู services
kubectl get services
kubectl get svc

# ดู deployments
kubectl get deployments
kubectl get deploy

# Apply YAML configuration
kubectl apply -f deployment.yaml

# ดู logs
kubectl logs <pod-name>
kubectl logs -f <pod-name>  # follow logs

# เข้าไปใน pod
kubectl exec -it <pod-name> -- bash

# ลบ resources
kubectl delete deployment nginx
kubectl delete service nginx
```

### การทำงานกับ Helm

```bash
# ติดตั้ง Helm (ถ้ายังไม่มี)
brew install helm

# เพิ่ม Helm repository
helm repo add stable https://charts.helm.sh/stable
helm repo update

# ค้นหา charts
helm search repo nginx

# ติดตั้ง chart
helm install my-nginx stable/nginx

# ดู releases
helm list

# Upgrade release
helm upgrade my-nginx stable/nginx

# ลบ release
helm uninstall my-nginx
```

## การตั้งค่าและปรับแต่ง

### แก้ไขการตั้งค่า Colima

```bash
# แก้ไข config ของ Colima
colima edit

# แก้ไข config ของ profile ที่กำหนด
colima edit --profile development

# หลังแก้ไข config ต้อง restart
colima stop
colima start
```

### SSH เข้า VM

```bash
# SSH เข้า Colima VM
colima ssh

# SSH เข้า VM ของ profile ที่กำหนด
colima ssh --profile development

# รันคำสั่งใน VM
colima ssh -- uname -a
colima ssh -- docker ps
```

### การดูและตั้งค่า Environment

```bash
# แสดง Docker context
docker context ls

# ตั้งค่า Docker context เป็น Colima
docker context use colima

# ดู Docker socket path
echo $DOCKER_HOST

# ตั้งค่า DOCKER_HOST manually (ถ้าจำเป็น)
export DOCKER_HOST="unix://${HOME}/.colima/default/docker.sock"
```

## การ Mount Volumes และ Directories

```bash
# Mount directory เพิ่มเติม (ต้องหยุด Colima ก่อน)
colima stop
colima start --mount ~/Projects:w  # w = writable
colima start --mount ~/Data:r      # r = read-only

# Mount หลาย directories
colima start \
  --mount ~/Projects:w \
  --mount ~/Documents:r \
  --mount /tmp:w

# ใช้ Docker volume mounts ตามปกติ
docker run -v $(pwd):/app nginx
docker run -v ~/data:/data ubuntu
```

## Performance และ Troubleshooting

### ตรวจสอบทรัพยากร

```bash
# ดูการใช้งานทรัพยากรของ Colima VM
colima ssh -- top
colima ssh -- free -h
colima ssh -- df -h

# ดู Docker stats
docker stats

# ดูขนาดของ images และ containers
docker system df
```

### ทำความสะอาด

```bash
# ลบ Docker resources ที่ไม่ใช้
docker system prune

# ลบทั้งหมดรวม volumes
docker system prune -a --volumes

# ลบ dangling images
docker image prune

# ลบ stopped containers
docker container prune

# ลบ unused volumes
docker volume prune
```

### Reset และ Restart

```bash
# Restart Colima
colima restart

# Restart พร้อมกำหนดค่าใหม่
colima restart --cpu 4 --memory 8

# ลบและสร้างใหม่ทั้งหมด
colima delete --force
colima start --cpu 4 --memory 8 --disk 100
```

## Integration กับ Tools อื่นๆ

### ใช้กับ VS Code

```bash
# ตั้งค่า Docker extension ใน VS Code ให้ชี้ไปที่ Colima
# เพิ่มใน VS Code settings.json:
{
  "docker.host": "unix:///Users/<username>/.colima/default/docker.sock"
}
```

### ใช้กับ Podman

```bash
# เริ่ม Colima พร้อม podman socket
colima start --runtime containerd

# ใช้ podman CLI (ต้องติดตั้งก่อน)
brew install podman
podman-remote system connection add colima \
  unix://${HOME}/.colima/default/podman.sock
```

### ใช้กับ Docker Compose V2

```bash
# Docker Compose V2 ทำงานกับ Colima โดยอัตโนมัติ
docker compose version

# ถ้ายังใช้ docker-compose v1
# ควรอัพเกรดเป็น v2
brew install docker-compose
```

## Tips และ Best Practices

```bash
# สร้าง alias เพื่อความสะดวก
echo 'alias col="colima"' >> ~/.zshrc
echo 'alias cols="colima status"' >> ~/.zshrc

# ตั้งค่า profile สำหรับงานต่างๆ
colima start --profile dev --cpu 2 --memory 4
colima start --profile prod --cpu 8 --memory 16 --kubernetes

# สำรองข้อมูล Docker volumes
docker run --rm -v myvolume:/data -v $(pwd):/backup \
  ubuntu tar czf /backup/myvolume-backup.tar.gz /data

# เช็คว่า Colima ใช้ resources เท่าไร
ps aux | grep colima
colima ssh -- free -h

# Enable experimental features
colima start --network-address  # รับ IP address คงที่
```

## Troubleshooting ทั่วไป

```bash
# ถ้า Docker ไม่ทำงาน
colima status
docker context ls
docker context use colima

# ถ้า Kubernetes ไม่ start
colima delete
colima start --kubernetes --cpu 4 --memory 8

# ถ้า disk เต็ม
docker system prune -a --volumes
colima delete
colima start --disk 200  # เพิ่มขนาด disk

# ถ้ามีปัญหากับ network
colima restart
colima ssh -- sudo systemctl restart docker

# Debug logs
colima start --verbose
colima ssh -- journalctl -u docker -f
```

## การอัพเกรดและ Updates

```bash
# อัพเกรด Colima
brew upgrade colima

# ตรวจสอบ version หลังอัพเกรด
colima version

# ถ้ามีปัญหาหลังอัพเกรด ลองสร้างใหม่
colima delete --force
colima start
```

## Resources และ Monitoring

```bash
# Real-time monitoring
colima ssh -- htop

# Network stats
colima ssh -- netstat -tuln
colima ssh -- ss -tuln

# Process monitoring
colima ssh -- ps aux | grep docker
colima ssh -- systemctl status docker

# Disk usage
colima ssh -- du -sh /var/lib/docker
colima ssh -- df -h /var/lib/docker
```
