# วิธีการติดตั้ง CKAN 2.9 จาก Package บน Ubuntu 18.04 และ 20.04

การติดตั้ง CKAN 2.9 จาก Package จะมี Service และ Port ที่ต้องใช้ดังนี้

| Service | Port | Used for |
| ------- | ---- | -------- |
| NGINX | 80 | Proxy |
| uWSGI | 8080 | Web Server |
| uWSGI | 8800 | DataPusher |
| Solr/Jetty | 8983 | Search |
| PostgreSQL | 5432 | Database |
| Redis | 6379 | Search |

### 1. Update Package ของ Ubuntu:
```sh
sudo apt-get update
```

### 2. ติดตั้ง Package ของ Ubuntu ที่ CKAN ต้องการ:
```sh
sudo apt-get install -y libpq5 redis-server nginx supervisor libpython2.7 git-core
```

### 3. ตั้งค่า python2 และ pip2:
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

### 4. ติดตั้งและตั้งค่า PostgreSQL
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

### 6. ดาวน์โหลดและติดตั้ง CKAN package ตามเวอร์ชั่นของ Ubuntu:
ตรวจสอบเวอร์ชั่นของ Ubuntu โดยใช้คำสั่ง 
```sh
cat /etc/os-release
```
- สำหรับ Ubuntu 20.04:
```sh
    wget http://packaging.ckan.org/python-ckan_2.9-py2-focal_amd64.deb
    sudo dpkg -i python-ckan_2.9-py2-focal_amd64.deb
```
- สำหรับ Ubuntu 18.04:
```sh
    wget http://packaging.ckan.org/python-ckan_2.9-bionic_amd64.deb
    sudo dpkg -i python-ckan_2.9-bionic_amd64.deb
```
### 7. ติดตั้งและตั้งค่า Solr
```sh
sudo useradd --user-group --shell /bin/false --home-dir /opt/jetty/temp jetty

sudo apt-get install -y solr-jetty

sudo mkdir /etc/systemd/system/jetty9.service.d

sudo vi /etc/systemd/system/jetty9.service.d/solr.conf
    [Service]
    ReadWritePaths=/var/lib/solr

sudo vi /etc/jetty9/start.ini
    jetty.host=127.0.0.1
    jetty.port=8983
```
```sh
sudo vi /etc/solr/solr-jetty.xml

#แก้ไข solr-jetty.xml ตามนี้

<?xml version="1.0"  encoding="ISO-8859-1"?>
<!DOCTYPE Configure PUBLIC "-//Jetty//Configure//EN" "http://www.eclipse.org/jetty/configure.dtd">

<!-- Context configuration file for the Solr web application in Jetty -->

<Configure class="org.eclipse.jetty.webapp.WebAppContext">
  <Set name="contextPath">/solr</Set>
  <Set name="war">/usr/share/solr/web</Set>

  <!-- Set the solr.solr.home system property -->
  <Call name="setProperty" class="java.lang.System">
    <Arg type="String">solr.solr.home</Arg>
    <Arg type="String">/usr/share/solr</Arg>
  </Call>

  <!-- Enable symlinks -->
  <!--<Call name="addAliasCheck">
    <Arg>
      <New class="org.eclipse.jetty.server.handler.ContextHandler$ApproveSameSuffixAliases"/>
    </Arg>
  </Call>-->
</Configure>
```
```sh
sudo mv /etc/solr/conf/schema.xml /etc/solr/conf/schema.xml.bak

sudo ln -s /usr/lib/ckan/default/src/ckan/ckan/config/solr/schema.xml /etc/solr/conf/schema.xml

sudo systemctl daemon-reload

sudo service jetty9 restart
```

### 8. ตั้งค่าและสร้างฐานข้อมูลสำหรับ CKAN
#### 8.1 ตั้งค่า who.ini:
```sh
#ตั้งค่า who.ini
sudo mv /etc/ckan/default/who.ini /etc/ckan/default/who.ini.bak

sudo ln -s /usr/lib/ckan/default/src/ckan/who.ini /etc/ckan/default/who.ini
```
#### 8.2 แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
sudo vi /etc/ckan/default/ckan.ini
    - เพิ่มค่า config ถัดจาก [app:main] (มีอยู่แล้ว)
        [app:main]
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
    - แก้ไข ckan.auth.public_user_details
        > ckan.auth.public_user_details = false
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

sudo supervisorctl reload
```

#### 8.3 เริ่มต้นสร้างฐานข้อมูลสำหรับ CKAN:
```sh
sudo ckan db init
```

### 9. cronjob page view tracking
```sh
crontab -e
```
เพิ่มคำสั่งต่อไปนี้

    @hourly /usr/lib/ckan/default/bin/ckan -c /etc/ckan/default/ckan.ini tracking update && /usr/lib/ckan/default/bin/ckan -c /etc/ckan/default/ckan.ini search-index rebuild -r

### 10. ปรับแต่งสิทธิ์ที่จำเป็น และ restart Apache Nginx และ Jetty
```sh
sudo rm -rf /etc/nginx/sites-enabled/ckan

sudo chown -R `whoami` /usr/lib/ckan/default

sudo chmod -R 775 /usr/lib/ckan/default/src/ckan/ckan/public

sudo chown -R www-data:www-data /usr/lib/ckan/default/src/ckan/ckan/public
```

### 11. สร้าง CKAN SysAdmin และกำหนดสิทธิ์ DataStore

```sh
cd /usr/lib/ckan

. /usr/lib/ckan/default/bin/activate

/usr/lib/ckan/default/bin/python -m pip install --upgrade pip

#เปลี่ยน {username}
ckan -c /etc/ckan/default/ckan.ini sysadmin add {username}

paster --plugin=ckan datastore set-permissions -c /etc/ckan/default/production.ini | sudo -u postgres psql --set ON_ERROR_STOP=1

deactivate

sudo supervisorctl reload

sudo service nginx restart
```

### 12. ทดสอบเรียกใช้เว็บไซต์ผ่าน http://{domain name} และ login ด้วย SysAdmin

### 13. ติดตั้งและตั้งค่า [CKAN Extensions](ckan-extension.md)
