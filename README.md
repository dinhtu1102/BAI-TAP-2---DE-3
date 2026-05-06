# BAI-TAP-2 - DE-3
# Họ tên : Nguyễn Đình Tú
# Mssv : K225480106067
# ĐỀ 3: MariaDB + Home Assistant (HASS) + HASS Add-on

# BÀI LÀM 

* Mục tiêu bài

Bạn cần triển khai hệ thống gồm:

MariaDB (database)

Home Assistant (HASS) (server chính)

HASS Add-on (kết nối DB hoặc mở rộng)

Chạy trên Docker / Docker Compose

Có thể truy cập qua IP hoặc domain (nếu yêu cầu

1 . Chuẩn bị môi trường

✔️ Trên máy ảo (Hyper-V / VMware / VirtualBox)

- máy ảo hyper-v
- 
<img width="1199" height="683" alt="image" src="https://github.com/user-attachments/assets/78bc3aca-33dc-423b-b98c-1a1ddb9b3cc6" />

Cài:

Ubuntu Server 20.04 hoặc 22.04

✔️ Cài Docker + Docker Compose

Chạy từng lệnh:

sudo apt update

<img width="1919" height="749" alt="image" src="https://github.com/user-attachments/assets/69e26310-8854-4635-8146-945d03db45eb" />

sudo apt install docker.io -y

<img width="1919" height="475" alt="image" src="https://github.com/user-attachments/assets/1e4b9de3-b911-49a2-bb74-8e08296e30bc" />

sudo systemctl enable docker

<img width="1918" height="215" alt="image" src="https://github.com/user-attachments/assets/3c8d616c-7322-40f4-8b77-7a53bfd296a6" />

sudo systemctl start docker

<img width="1919" height="150" alt="image" src="https://github.com/user-attachments/assets/8768ec7f-946f-49b8-8c7a-bdeb6c00caba" />

sudo apt install docker-compose -y

<img width="1919" height="856" alt="image" src="https://github.com/user-attachments/assets/63a18105-5ddb-46c9-8b63-ea0ebb6bca25" />

* Kiểm tra:

docker --version

<img width="1919" height="190" alt="image" src="https://github.com/user-attachments/assets/43181852-a363-47b9-8163-54619e5a3873" />

docker-compose --version

<img width="1915" height="248" alt="image" src="https://github.com/user-attachments/assets/a5a91af0-f9fb-4b02-b8ea-0414fd39e0cb" />

2. Lấy IP máy ảo

ip a

<img width="1919" height="719" alt="image" src="https://github.com/user-attachments/assets/83464058-e8eb-48ef-97ed-7273cd747232" />

👉 Tìm dòng:

inet 172.24.199.96/20

→ Đây là IP để truy cập HASS

3: TRIỂN KHAI HASS + MARIADB

3.1 Tạo project

mkdir hass

<img width="1913" height="232" alt="image" src="https://github.com/user-attachments/assets/fc8e1854-337f-4296-af39-e6ac8eadbe71" />

cd hass

<img width="1919" height="111" alt="image" src="https://github.com/user-attachments/assets/d4f36666-f453-401e-a46b-d4752c68d12c" />

nano docker-compose.yml

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/7acc3cbd-7728-4f63-8767-47ce0d07b338" />

3.2 Nội dung docker-compose
version: '3.7'

services:
  mariadb:
    image: mariadb:10.6
    container_name: mariadb
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: 123456
      MYSQL_DATABASE: homeassistant
      MYSQL_USER: hass
      MYSQL_PASSWORD: hass123
    volumes:
      - ./db:/var/lib/mysql
    ports:
      - "3306:3306"

  homeassistant:
    image: ghcr.io/home-assistant/home-assistant:stable
    container_name: hass
    restart: always
    network_mode: host
    volumes:
      - ./config:/config
      - /etc/localtime:/etc/localtime:ro
3.3 Chạy hệ thống

docker-compose up -d

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/c949e919-d0e5-4c73-976e-75273b95c560" />

3.4 Truy cập HASS

Mở trình duyệt:

[http://IP:8123](http://172.24.199.96/20:8123)

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/76019352-31f3-4e94-8057-90fe0a54fcea" />

👉 Tạo tài khoản

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/4d23091b-23cd-48dc-8765-344682cc28fe" />

<img width="1919" height="1062" alt="image" src="https://github.com/user-attachments/assets/14a14ed9-8113-4104-93eb-1691027c9976" />

4: KẾT NỐI HASS VỚI MARIADB

Sửa file:

nano config/configuration.yaml

Thêm:

recorder:

  db_url: mysql://hass:hass123@127.0.0.1/homeassistant?charset=utf8mb4

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/9254682a-d754-4938-be9d-53aebb176e33" />

Restart:

docker restart hass

<img width="1915" height="129" alt="image" src="https://github.com/user-attachments/assets/d1f6e1f1-285d-42ee-99e4-2d0e211a6346" />

<img width="1914" height="1024" alt="image" src="https://github.com/user-attachments/assets/d4c8f774-e048-45df-94d3-7f640b40a145" />

* Cấu hình file config.yml

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/c3dccf02-f4af-44f3-945c-924b2501a92a" />

* Định tuyến tên miền

Vì bạn có nhiều tunnel (odoo, wp, nangluong...), lệnh này sẽ khẳng định domain dinhtu.id.vn phải trỏ về đúng tunnel của Home Assistant:

Bash

cloudflared tunnel route dns hass-tunnel dinhtu.id.vn

<img width="1917" height="123" alt="image" src="https://github.com/user-attachments/assets/093bd213-8881-4358-89a6-4f49ef41b074" />

* Chạy Tunnel
  
Bây giờ Tú hãy cho hệ thống "lên sóng":

Bash

cloudflared tunnel run hass-tunnel

<img width="1919" height="1008" alt="image" src="https://github.com/user-attachments/assets/e934ad32-db43-421f-840a-8fa6c35cc72c" />

6 CẤU HÌNH DOMAIN VỚI Cloudflare

6.1 Thêm domain vào Cloudflare

Vào Cloudflare

Add site: dinhtu.id.vn

6.2 Đổi nameserver

Cloudflare cấp:

xxx.ns.cloudflare.com

👉 Bạn vào nơi mua domain → đổi sang Cloudflare

 7: CLOUDLFARE TUNNEL (QUAN TRỌNG NHẤT)

7.1 Cài cloudflared

sudo apt install cloudflared -y

7.2 Login

cloudflared tunnel login

👉 Chọn domain dinhtu.id.vn

7.3 Tạo tunnel

cloudflared tunnel create hass-tunnel

7.4 Tạo config

nano ~/.cloudflared/config.yml

tunnel: hass-tunnel

credentials-file: /root/.cloudflared/xxxxx.json

ingress:

  - hostname: dinhtu.id.vn
  - 
    service: http://localhost:8123
    
  - service: http_status:404

7.5 Gắn domain
    
cloudflared tunnel route dns hass-tunnel dinhtu.id.vn

7.6 Chạy tunnel

cloudflared tunnel run hass-tunnel

🌍 PHẦN 8: TRUY CẬP WEBSITE

Mở:

http://dinhtu.id.vn

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/68524a16-e2bf-4440-bdc6-f6d19cc3f11a" />


👉 Thành công sẽ vào Home Assistant 🎉

🔒 PHẦN 9: BẬT HTTPS

Trong Cloudflare:

SSL/TLS → chọn:

Full

<img width="1903" height="1029" alt="image" src="https://github.com/user-attachments/assets/d690fc60-b1b8-4bde-a7af-c75abdeffd7b" />

* Giao diện đang nhập

<img width="1911" height="1030" alt="image" src="https://github.com/user-attachments/assets/b5bb8a30-0819-4c03-a804-02e8bb0a003d" />

* Giao diện trang chủ 

<img width="1913" height="1027" alt="image" src="https://github.com/user-attachments/assets/46daf59d-ca38-419a-8497-88b26b0c6c06" />

