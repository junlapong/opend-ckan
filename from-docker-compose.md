# วิธีการติดตั้ง CKAN ด้วย Docker Compose
สำหรับการติดตั้ง CKAN ด้วย Docker Compose แนะนำให้มีการติดตั้ง docker และ docker-compose ก่อน โดยใช้เวอร์ชั่น ดังนี้ 
- docker >= 19
- docker-compose >= version 1.13

วิธีการตรวจสอบเวอร์ชั่น docker และ docker-compose
```sh
docker -v
#Docker version 19.03.13, build 4484c46d9d

docker-compose -v
#docker-compose version 1.26.2, build unknown
```
## การติดตั้ง docker
ติดตั้ง docker สำหรับเครื่องที่ยังไม่ได้ติดตั้ง โดยใช้คำสั่ง
```sh
curl https://get.docker.com | sh
#รอจนกว่าการติดตั้งเสร็จเรียบร้อย
```
เมื่อติดตั้ง docker เรียบร้อยให้ทำการให้สิทธิ์การใช้งานสำหรับ user ตั้งนี้
```sh
sudo newgrp docker

sudo usermod -aG docker `whoami`

#ตรวจสอบ image ที่มีอยู่ในเครื่อง
docker images

#ทดสอบ run docker 
docker run hello-world
```

## การติดตั้ง docker-compose
ติดตั้ง docker-compose สำหรับเครื่องที่ยังไม่ได้ติดตั้ง โดยใช้คำสั่ง
```sh
sudo apt install docker-compose

# ตรวจสอบ version ของ docker-compose
docker-compose -v
```

## การติดตั้ง ckan docker และ extension
### 1. ทำการโหลด ckan-docker-thai-gdc
```sh
git clone https://gitlab.nectec.or.th/opend/ckan-docker-thai-gdc.git ~/ckan-docker
```

### 2. ทำสร้างไฟล์ .env จากไฟล์ .env.template ที่เตรียมไว้ให้
```sh
cd ~/ckan-docker

cp .env.tempate .env
```
### 3. แก้ไขไฟล์ .env
```sh
    - กำหนด Username และ Password สำหรับ Database ของ CKAN
        > POSTGRES_USER=ckan
        > POSTGRES_PASSWORD={ckan_password}
    - กำหนด Username และ Password สำหรับ Datastore
        > DATASTORE_READONLY_USER=datastore_ro
        > DATASTORE_READONLY_PASSWORD={datastore_password}
    - กำหนดชื่อ Host สำหรับ Database Postgres
        > POSTGRES_HOST=db
    - กำหนด version ของ CKAN
        > CKAN_VERSION=2.8
    - ตัวเลขกำกับ container (default)
        > PROJECT_NUMBER=1
    - กำหนด port สำหรับ Nginx
        > NGINX_PORT=8881
    - กำหนด port สำหรับ Datapusher
        > DATAPUSHER_PORT=8800
    - กำหนด url สำหรับเว็บเป็น Domain/IP
        > DEFAULT_URL=http://localhost
    - กำหนด CKAN Site ID (default)
        > CKAN_SITE_ID=default
    - กำหนด CKAN Site URL
        > CKAN_SITE_URL=http://{domain}:8881
    - กำหนด CKAN Port
        > CKAN_PORT=5000
    - กำหนดรายละเอียด SysAdmin ของระบบ
        > CKAN_SYSADMIN_NAME={admin_username}
        > CKAN_SYSADMIN_PASSWORD={admin_password}
        > CKAN_SYSADMIN_EMAIL={admin_email}
    - การตั้งค่าเพื่อจัดการ database postgres สำหรับ CKAN
        > CKAN_SQLALCHEMY_URL=postgresql://ckan:ckan@db/ckan
    - การตั้งค่าเพื่อเขียนข้อมูลลง datastore สำหรับ plugin dataphser
        > CKAN_DATASTORE_WRITE_URL=postgresql://ckan:ckan@db/datastore
    - การตั้งค่าเพื่ออ่านข้อมูลจาก datastore สำหรับ plugin dataphser
        > CKAN_DATASTORE_READ_URL=postgresql://datastore_ro:datastore@db/datastore
    - url สำหรับเชื่อมต่อกับ solr
        > CKAN_SOLR_URL=http://solr:8983/solr/ckan
    - url สำหรับเชื่อมต่อกับ redis
        > CKAN_REDIS_URL=redis://redis:6379/0
    - path สำหรับ storage ของ CKAN
        > CKAN__STORAGE_PATH=/var/lib/ckan
    - plugin ทั้งหมดที่เปิดใช้งาน
        > CKAN__PLUGINS=envvars stats image_view text_view recline_view resource_proxy webpage_view datastore datapusher scheming_datasets pdf_view hierarchy_display hierarchy_form dcat dcat_json_interface structured_data thai_gdc
    - defualt view
        > CKAN__VIEWS__DEFAULT_VIEWS=image_view text_view recline_view webpage_view pdf_view
    - default dataset schema
        > CKAN___SCHEMING__DATASET_SCHEMAS=ckanext.thai_gdc:ckan_dataset.json
```

### 4. เริ่มการทำงานของ CKAN ด้วย docker-compose
```sh
$ docker-compose up -d --build
# ตรวจการทำงานของ docker-compose ที่เรารัน
$ docker ps 
###########
ทดสอบการงานของ CKAN ด้วยเข้าไปใช้งานเว็บบราวเซอร์ url: http://localhost:8881
```

### 5. ยกเลิกการทำงานของ CKAN docker และ clear ข้อมูล 
```sh
$ docker-compose down
$ docker volume prune
$ docker system prune
```
