# วิธีการติดตั้ง CKAN 2.8 จาก Package บน Ubuntu 16.04

การติดตั้ง CKAN 2.8 จาก Package จะมี Service และ Port ที่ต้องใช้ดังนี้

| Service | Port | Used for |
| ------- | ---- | -------- |
| NGINX | 80 | Proxy |
| Apache2 | 8080 | Web Server |
| Solr/Jetty | 8983 | Search |
| PostgreSQL | 5432 | Database |
| Redis | 6379 | Search |

### 1. Update Package ของ Ubuntu:
```sh
sudo apt-get update
```

### 2. ตั้งค่า python2 และ pip2:
```sh
#ตรวจสอบเวอร์ชั่นของ python และตั้งค่า default python2
python -V
    # หากยังไม่ใช่ version 2 ใช้คำสั่งนี้ และ select python2
    sudo update-alternatives --config python

#ตรวจสอบเวอร์ชั่นของ pip และตั้งค่า default pip for python 2
pip -V
    # หากยังไม่ใช่ version 2 ใช้คำสั่งนี้
    sudo cp /usr/local/bin/pip2 /usr/local/bin/pip
```

### 3. ติดตั้ง Package ของ Ubuntu ที่ CKAN ต้องการ:
```sh
sudo apt-get install -y nginx apache2 libapache2-mod-wsgi libpq5 redis-server git-core
```

### 4. ติดตั้งและตั้งค่า PostgreSQL:
```sh
sudo apt-get install -y postgresql

# สร้าง postgres user สำหรับ ckan_default และใส่ password1
sudo -u postgres createuser -S -D -R -P ckan_default

# สร้างฐานข้อมูล ckan_default
sudo -u postgres createdb -O ckan_default ckan_default -E utf-8

# สร้าง postgres user สำหรับ datastore_default และใส่ password2
sudo -u postgres createuser -S -D -R -P -l datastore_default

# สร้างฐานข้อมูล datastore_default
sudo -u postgres createdb -O ckan_default datastore_default -E utf-8

#ตรวจสอบ database list ให้มี database ckan_default และ datastore_default
sudo -u postgres psql -l
```

### 5. ตั้งค่า Apache, Nginx และ Storage path:
```sh
cd ~

#ตั้งค่า Apache
wget https://gitlab.nectec.or.th/opend/installing-ckan/-/raw/master/config/apache/ckan_default.conf -P ./apache

sudo cp ./apache/ckan_default.conf /etc/apache2/sites-available/ckan_default.conf

#ตั้งค่า Nginx
wget https://gitlab.nectec.or.th/opend/installing-ckan/-/raw/master/config/nginx/ckan_default.conf -P ./nginx

sudo cp ./nginx/ckan_default.conf /etc/nginx/conf.d/ckan_default.conf

#เตรียม proxycache
sudo mkdir -p /var/cache/nginx/proxycache && sudo chown www-data /var/cache/nginx/proxycache

#เตรียม storage path
sudo mkdir -p /var/lib/ckan/default/storage

sudo chown -R www-data:www-data /var/lib/ckan && sudo chmod -R 775 /var/lib/ckan
```

### 6. ติดตั้งและตั้งค่า Solr:
```sh
sudo useradd --user-group --shell /bin/false --home-dir /opt/jetty/temp jetty

sudo apt-get install -y solr-jetty

sudo vi /etc/default/jetty8
    NO_START=0            # (line 4)
    JETTY_HOST=127.0.0.1  # (line 16)
    JETTY_PORT=8983       # (line 19)

sudo rm -rf /etc/solr/conf/schema.xml

sudo ln -s /usr/lib/ckan/default/src/ckan/ckan/config/solr/schema.xml /etc/solr/conf/schema.xml

sudo service jetty8 restart
```

### 7. ดาวน์โหลดและติดตั้ง CKAN package ตามเวอร์ชั่นของ Ubuntu:
```sh
#สำหรับ Ubuntu 16.04:
wget http://packaging.ckan.org/python-ckan_2.8-xenial_amd64.deb
sudo dpkg -i python-ckan_2.8-xenial_amd64.deb
```

### 8. ตั้งค่าและสร้างฐานข้อมูลสำหรับ CKAN:
#### 8.1 ตั้งค่า who.ini และ apache.wsgi:
```sh
#ตั้งค่า who.ini
sudo rm -rf /etc/ckan/default/who.ini

sudo ln -s /usr/lib/ckan/default/src/ckan/who.ini /etc/ckan/default/who.ini

#ตั้งค่า apache.wsgi
wget https://gitlab.nectec.or.th/opend/installing-ckan/-/raw/master/config/apache/apache.wsgi -P ./apache

sudo cp ./apache/apache.wsgi /etc/ckan/default/apache.wsgi
```

#### 8.2 แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
sudo vi /etc/ckan/default/production.ini
    - เพิ่มค่า config ถัดจาก [app:main] (มีอยู่แล้ว)
        [app:main]
        ckan.auth.public_user_details = false
        ckan.tracking_enabled = true
    - แก้ไข password ของ sqlalchemy.url
        > sqlalchemy.url = postgresql://ckan_default:{password1}@localhost/ckan_default
    - เปิดการใช้งาน และแก้ไข password ของ ckan.datastore.write_url
        > ckan.datastore.write_url = postgresql://ckan_default:{password1}@localhost/datastore_default
    - เปิดการใช้งาน และแก้ไข password ของ ckan.datastore.read_url
        > ckan.datastore.read_url = postgresql://datastore_default:{password2}@localhost/datastore_default
    - กำหนด ip หรือ domain name ที่ ckan.site_url
        > ckan.site_url = http://{domain name}
    - แก้ไข ckan.auth.user_delete_groups
        > ckan.auth.user_delete_groups = false
    - แก้ไข ckan.auth.user_delete_organizations
        > ckan.auth.user_delete_organizations = false
    - เปิดการใช้งาน solr_url
        > solr_url = http://127.0.0.1:8983/solr
    - เปิดการใช้งาน ckan.redis.url
        > ckan.redis.url = redis://localhost:6379/0
    - แก้ไข ckan.plugins (ให้เหมือนตามนี้)
        > ckan.plugins = stats text_view image_view recline_view resource_proxy datastore datapusher webpage_view
    - แก้ไข ckan.views.default_views (ให้เหมือนตามนี้)
        > ckan.views.default_views = image_view text_view recline_view webpage_view
    - แก้ไข ckan.locale_default
        > ckan.locale_default = th
    - แก้ไข ckan.locale_order (แทรก th)
        > ckan.locale_order = en th pt_BR ...(ต่อจากนั้นเหมือนเดิม)
    - เปิดการใช้งานและแก้ไข ckan.storage_path
        > ckan.storage_path = /var/lib/ckan/default
    - เปิดการใช้งาน ckan.datapusher.formats
        > ckan.datapusher.formats = csv ...(ต่อจากนั้นเหมือนเดิม)
    - เปิดการใช้งาน ckan.datapusher.url
        > ckan.datapusher.url = http://127.0.0.1:8800/
    - เปิดการใช้งานและแก้ไข ckan.datapusher.assume_task_stale_after
        > ckan.datapusher.assume_task_stale_after = 60
    - เปิดการใช้งาน ckan.activity_streams_enabled
        > ckan.activity_streams_enabled = true

sudo service apache2 restart
```

#### 8.3 เริ่มต้นสร้างฐานข้อมูลสำหรับ CKAN:
```sh
sudo ckan db init
```

### 9. สร้าง cronjob page view tracking:
```sh
crontab -e
```
เพิ่มคำสั่งต่อไปนี้

    @hourly /usr/lib/ckan/default/bin/paster --plugin=ckan tracking update -c /etc/ckan/default/production.ini && /usr/lib/ckan/default/bin/paster --plugin=ckan search-index rebuild -r -c /etc/ckan/default/production.ini

### 10. ปรับแต่งสิทธิ์ที่จำเป็น และ restart Apache Nginx และ Jetty:
```sh
sudo rm -rf /etc/nginx/sites-enabled/ckan

sudo chown -R `whoami` /usr/lib/ckan/default

sudo chmod -R 775 /usr/lib/ckan/default/src/ckan/ckan/public && sudo chown -R www-data:www-data /usr/lib/ckan/default/src/ckan/ckan/public
```

### 11. สร้าง CKAN SysAdmin และกำหนดสิทธิ์ DataStore:

```sh
cd /usr/lib/ckan/default/src/ckan

. /usr/lib/ckan/default/bin/activate

/usr/lib/ckan/default/bin/python -m pip install --upgrade pip

#เปลี่ยน {username}
paster sysadmin add {username} -c /etc/ckan/default/production.ini

paster --plugin=ckan datastore set-permissions -c /etc/ckan/default/production.ini | sudo -u postgres psql --set ON_ERROR_STOP=1

deactivate

sudo service apache2 restart

sudo service nginx restart

sudo service jetty8 restart
```

### 12. ทดสอบเรียกใช้เว็บไซต์ผ่าน http://{domain name} และ login ด้วย SysAdmin

### 13. ติดตั้งและตั้งค่า [CKAN Extensions](ckan-extension.md)

