# วิธีการติดตั้ง CKAN ด้วย Docker Compose
สำหรับการติดตั้ง CKAN ด้วย Docker Compose แนะนำให้มีการติดตั้ง docker และ docker-compose ก่อน โดยใช้เวอร์ชั่น ดังนี้ 
- docker >= 19
- docker-compose >= version 1.17

### วิธีการตรวจสอบเวอร์ชั่น docker และ docker-compose

```sh
$ docker -v
Docker version 19.03.13, build 4484c46d9d
$ docker-compose -v
docker-compose version 1.26.2, build unknown
```
## การติดตั้ง docker
ติดตั้ง docker สำเครื่องที่ยังไม่ได้ติดตั้ง
```sh
$ sudo curl https://get.docker.com | sh
#รอจนกว่าการติดตั้งจะเสร็จเรียบร้อย
```
เมื่อติดตั้ง docker เรียบร้อยให้ทำการให้สิทธิการใช้งานสำหรับ user ตั้งนี้
```sh
$ sudo newgrp docker
$ sudo usermode -aG docker `whoami`
# ตรวจสอบ image ที่มีอยู่ในเครื่อง
$ docker images
# ทดสอบ run docker 
$ docker run hello-world
```

## การติดตั้ง docker-compose
```sh
$ sudo apt install docker-compose
# ตรวจสอบ version ของ docker-compose
$ docker-compose -v
```

## การติดตั้ง ckan docker สำหรับ thai gdc
1. ทำการ ckan-docker-thai-gdc ที่เตรียมไว้ให้
```sh
$ git clone https://gitlab.nectec.or.th/opend/ckan-docker-thai-gdc.git ~/ckan-docker
```
2. ทำสร้างไฟล์ .env จากไฟล์ .env.template ที่เตรียมไว้ให้
```sh
$ cd ~/ckan-docker
$ cp .env.tempate .env
```
3. แก้ไฟล์ .env
```sh
# DB image settings
# POSTGRES_USER must be set to the database user used by CKAN, typically named ckan
# Readwrite user/pass will be POSTGRES_USER:POSTGRES_PASSWORD
# ตัวแปรที่ใช้สร้าง username password  สำหรับ database ของ ckan
POSTGRES_USER=ckan
POSTGRES_PASSWORD=ckan
# Readonly user/pass will be DATASTORE_READONLY_USER:DATASTORE_READONLY_PASSWORD
# ตัวแปรที่ใช้สร้าง username password สำหรับ datastore
DATASTORE_READONLY_USER=datastore_ro
DATASTORE_READONLY_PASSWORD=datastore
# POSTGRES_HOST must be set to the host for the postgres server.
# As a default 'db' will point to the postgres running in the docker container specified by the docker-compose.yml file.
# ชื่อ host  สำหรับ databese postgres
POSTGRES_HOST=db
# เวอร์ชั่นของ CKAN 2.8
# ยังสามารถใช้ได้เฉพาะ 2.8 อยู่ครับ
CKAN_VERSION=2.8
# ตัวเลขกำกับสำหรับรัน container หลายๆ site
PROJECT_NUMBER=1
# port สำหรับ nginx
NGINX_PORT=8881
# port สำหรับ datapusher
DATAPUSHER_PORT=8800
# เป็น url สำหับเว็บของเราอาจจะใช้เป็น domain name / ip assress ไปก่อน
DEFAULT_URL=http://localhost

# Basic
CKAN_SITE_ID=default
# CKAN_SITE_URL=http://host.docker.internal:8881
# CKAN_SITE_URL=http://localhost:8881
CKAN_PORT=5000
# สำหรับสร้าง sysadmin ของระบบ
CKAN_SYSADMIN_NAME=ckan_admin
CKAN_SYSADMIN_PASSWORD=test1234
CKAN_SYSADMIN_EMAIL=your_email@example.com
TZ=Asia/Bangkok

# Database connections (TODO: avoid duplication)
# การตั้งค่าเพื่อจัดการ database postgres สำหรับ CKAN
CKAN_SQLALCHEMY_URL=postgresql://ckan:ckan@db/ckan
# การตั้งค่าเพื่อเขียนข้อมูลลง datastore สำหรับ plugin dataphser
CKAN_DATASTORE_WRITE_URL=postgresql://ckan:ckan@db/datastore
# การตั้งค่าเพื่ออ่านข้อมูลจาก datastore สำหรับ plugin dataphser
CKAN_DATASTORE_READ_URL=postgresql://datastore_ro:datastore@db/datastore

# Other services connections
# url สำหรับเชื่อมต่อกับ solr
CKAN_SOLR_URL=http://solr:8983/solr/ckan
# url สำหรับเชื่อมต่อกับ redis
CKAN_REDIS_URL=redis://redis:6379/0


# Core settings
# path สำหรับ storage ของ CKAN
CKAN__STORAGE_PATH=/var/lib/ckan
CKAN_SMTP_SERVER=smtp.corporateict.domain:25
CKAN_SMTP_STARTTLS=True
CKAN_SMTP_USER=user
CKAN_SMTP_PASSWORD=pass
CKAN_SMTP_MAIL_FROM=ckan@localhost

# Extensions
# plugin ทั้งหมดที่เปิดใช้งาน
CKAN__PLUGINS=envvars stats image_view text_view recline_view resource_proxy webpage_view datastore datapusher scheming_datasets pages pdf_view hierarchy_display hierarchy_form dcat dcat_json_interface structured_data thai_gdc
#thai_gdc scheming_datasets scheming_datasets
CKAN__HARVEST__MQ__TYPE=redis
CKAN__HARVEST__MQ__HOSTNAME=redis
CKAN__HARVEST__MQ__PORT=6379
CKAN__HARVEST__MQ__REDIS_DB=0

# CKAN__SITE_URL=http://localhost:8881

# defualt view
CKAN__VIEWS__DEFAULT_VIEWS=image_view text_view recline_view webpage_view pdf_view

# pages extensin setting 
CKANEXT__PAGES__FORM=pages/base_form.html
# CKANEXT__PAGES__ABOUT_MENU=False
# CKANEXT__PAGES__GROUP_MENU=False
# CKANEXT__PAGES__ORGANIZATION_MENU=False
CKANEXT__PAGES__ALLOW_HTML=True
CKANEXT__PAGES__EDITOR=ckeditor
# dataset schema setting
CKAN___SCHEMING__DATASET_SCHEMAS=ckanext.thai_gdc:ckan_dataset.json
```

4. เริ่มการทำงานของ CKAN ด้วย docker-compose
```sh
$ docker-compose up -d --build
# ตรวจการทำงานของ docker-compose ที่เรารัน
$ docker ps 
###########
ทดสอบการงานของ CKAN ด้วยเข้าไปใช้งานเว็บบราวเซอร์ url: http://localhost:8881
```

5. ยกเลิกการทำงานของ CKAN docker และ clear ข้อมูล 
```sh
$ docker-compose down
$ docker volume prune
$ docker system prune
```
