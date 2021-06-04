# วิธีการติดตั้ง ssl สำหรับ ckan
### 1.การสร้าง/ขอ ssl certificate จากเว็บผู้ให้บริการ
---
ให้ทำการสร้าง/ขอ ssl certficate จากเว็บผู้ให้บริการที่ท่านเลือกใช้งาน และทำการดาวน์โหลดไฟล์ที่ใช้สำหรับการ ติดตั้ง ssl certicate บน server ของท่านดังนี้ 
```
1. your-domain-cert.crt/.pem
# ไฟล์ ssl certificate สำหรับ domain ของท่านที่เป็นไฟล์นามสกุล .crt/.pem 

2. your-domain.key
# ไฟล์ private key ที่ท่านใช้สำหรับสร้าง certificate

3. RootCA-ca.crt / Intermediate-CA.crt
# เป็นไฟล์ certificate ที่ออกโดยเว็บผู้ให้บริการที่ท่านเลือกใช้งานเพื่อเป็นการยืนยันตัวตนว่าท่านเป็นเจ้าของ certificate ที่ใช้
```
### 2.อัพโหลดไฟล์ที่ได้ไปยัง server ของท่าน
```
sudo mkdir -p /etc/nginx/ssl/domain
# สร้าง directory เพื่อเก็บไฟล์ certificate และทำการอัพโหลดไฟยัง directory
```

### 3.รวมไฟล์ domain-certificate และ CA certificate
```
sudo cd /etc/nginx/ssl/domain
sudo cat your-domain-cert.crt RootCA-ca.crt >> cert_bundle.crt
```

### 4.ทำการแก้ไขไฟล์ config nginx ของ ckan
```
### ตรวจสอบให้แน่ใจว่าไฟล์ nginx config สำหรับ ckan ติดตั้งไว้ที่ตำแหน่งใด
# สำหรับติดตั้งแบบ source
sudo nano /etc/nginx/sites-available/ckan
# สำหรับติดตั้งแบบ package
sudo nano /etc/nginx/conf.d/ckan_default.conf
```
แก้ไข confi ตามนี้

```
proxy_cache_path /tmp/nginx_cache levels=1:2 keys_zone=cache:30m max_size=250m;
proxy_temp_path /tmp/nginx_proxy 1 2;

server {
    client_max_body_size 100M;
    server_name ##เปลี่ยนเป็น domain ของท่าน##;
    location / {
        proxy_pass http://127.0.0.1:8080/;
        proxy_set_header Host $host;
        proxy_cache cache;
        proxy_cache_bypass $cookie_auth_tkt;
        proxy_no_cache $cookie_auth_tkt;
        proxy_cache_valid 30m;
        proxy_cache_key $host$scheme$proxy_host$request_uri;
    }

    listen 443 ssl; 
    ssl_certificate /etc/nginx/ssl/domain/cert_bundle.crt; ## เปลี่ยนเป็น certificate ของท่าน
    ssl_certificate_key /etc/nginx/ssl/domain/your-domain.key; ## เปลี่ยนเป็น private key ของท่าน

}

server {
    listen 80;
    server_name ##เปลี่ยนเป็น domain ของท่าน##;
    return 301 https://$host$request_uri;
}
```
ทำการบันทึกและทดสอบ config ของท่าน 
```
sudo nginx -t
```
เมื่อทดสอบแล้วไม่มีข้อผิดพลาดให้ทำการ restart nginx

```
sudo service nginx restart
```

> ท่านสามารถทดสอบการติดตั้ง ssl certificate ของท่านได้ที่ https://www.ssllabs.com/ssltest/