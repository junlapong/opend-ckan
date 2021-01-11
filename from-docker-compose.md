# วิธีการติดตั้ง CKAN ด้วย Docker Compose
สำหรับการติดตั้ง CKAN ด้วย Docker Compose แนะนำให้ติดตั้ง docker และ docker-compose ก่อน โดยใช้เวอร์ชั่น ดังนี้ 
- docker >= 19
- docker-compose >= version 1.13

วิธีการตรวจสอบเวอร์ชั่นของ docker และ docker-compose
```sh
docker -v
#Docker version 19.03.13, build 4484c46d9d

docker-compose -v
#docker-compose version 1.26.2, build unknown
```
## [เตรียม] การติดตั้ง docker
ติดตั้ง docker โดยใช้คำสั่ง
```sh
curl https://get.docker.com | sh
#รอจนกว่าการติดตั้งเสร็จเรียบร้อย
```
ทำการให้สิทธิ์การใช้งานสำหรับ user
```sh
sudo newgrp docker

sudo usermod -aG docker `whoami`
```
ตรวจสอบ version ของ docker
```sh
docker -v
```

## [เตรียม] การติดตั้ง docker-compose
ติดตั้ง docker-compose โดยใช้คำสั่ง
```sh
sudo apt install docker-compose
```
ตรวจสอบ version ของ docker-compose
```sh
docker-compose -v
```

## การติดตั้ง CKAN Docker และ Extension
### 1. ดาวน์โหลด ckan-docker-thai-gdc
```sh
git clone https://gitlab.nectec.or.th/opend/ckan-docker-thai-gdc.git ~/ckan-docker
```

### 2. สร้างไฟล์ .env จากไฟล์ .env.template ที่เตรียมไว้ให้
```sh
cd ~/ckan-docker

cp .env.template .env
```
### 3. แก้ไขไฟล์ .env
```sh
vi .env
    - กำหนด Username และ Password สำหรับ Database ของ CKAN
        > POSTGRES_USER=ckan
        > POSTGRES_PASSWORD={ckan_password}
    - กำหนด Username และ Password สำหรับ Datastore
        > DATASTORE_READONLY_USER=datastore_ro
        > DATASTORE_READONLY_PASSWORD={datastore_password}
    - กำหนดชื่อ Host สำหรับ Database Postgres
        > POSTGRES_HOST=db
    - กำหนด version ของ CKAN
        > CKAN_VERSION=2.9
    - ตัวเลขกำกับ container (default)
        > PROJECT_NUMBER=1
    - กำหนด port สำหรับ Nginx
        > NGINX_PORT=8881
    - กำหนด port สำหรับ Datapusher
        > DATAPUSHER_PORT=8800
    - กำหนด url สำหรับเว็บเป็น Domain/IP
        > DEFAULT_URL=http://{Domain/IP}
    - กำหนด CKAN Site ID (default)
        > CKAN_SITE_ID=default
    - กำหนด CKAN Site URL
        > CKAN_SITE_URL=http://localhost:8881
    - กำหนด CKAN Port
        > CKAN_PORT=5000
    - กำหนดรายละเอียด SysAdmin ของระบบ
        > CKAN_SYSADMIN_NAME={admin_username}
        > CKAN_SYSADMIN_PASSWORD={admin_password}
        > CKAN_SYSADMIN_EMAIL={admin_email}
    - การตั้งค่าเพื่อจัดการ database postgres สำหรับ CKAN
        > CKAN_SQLALCHEMY_URL=postgresql://ckan:{ckan_password}@db/ckan
    - การตั้งค่าเพื่อเขียนข้อมูลลง datastore สำหรับ plugin dataphser
        > CKAN_DATASTORE_WRITE_URL=postgresql://ckan:{ckan_password}@db/datastore
    - การตั้งค่าเพื่ออ่านข้อมูลจาก datastore สำหรับ plugin xloader
        > CKAN_DATASTORE_READ_URL=postgresql://datastore_ro:{datastore_password}@db/datastore
        > CKANEXT__XLOADER__JOBS_DB__URI=postgresql://ckan:{ckan_password}@db/ckan
    - url สำหรับเชื่อมต่อกับ solr
        > CKAN_SOLR_URL=http://solr:8983/solr/ckan
    - url สำหรับเชื่อมต่อกับ redis
        > CKAN_REDIS_URL=redis://redis:6379/0
    - path สำหรับ storage ของ CKAN
        > CKAN__STORAGE_PATH=/var/lib/ckan
    - plugin ทั้งหมดที่เปิดใช้งาน
        > CKAN__PLUGINS=envvars stats image_view text_view recline_view resource_proxy webpage_view datastore xloader scheming_datasets pdf_view hierarchy_display hierarchy_form dcat dcat_json_interface structured_data thai_gdc
    - defualt view
        > CKAN__VIEWS__DEFAULT_VIEWS=image_view text_view recline_view webpage_view pdf_view
```

### 4. เริ่มการทำงานของ CKAN ด้วย docker-compose
```sh
docker-compose up -d --build

# ตรวจการทำงานของ docker-compose ที่ทำการ run อยู่
$ docker ps
```

### 5. ทดสอบเรียกใช้เว็บไซต์ผ่าน http://{Domain/IP}

#### การยกเลิกการทำงานของ CKAN docker และ clear ข้อมูล 
```sh
# คำสั่งสำหรับหยุดการทำงานและลบ docker container ที่อยู่ใน docker compose
$ docker-compose down
# คำสั่งสำหรับลบ volume ทั้งหมดที่ไม่ได้ใช้งาน
$ docker volume prune
# คำสั่งสำหรับลบ docker image ที่ไม่ได้ใช้งาน
$ docker system prune
```
