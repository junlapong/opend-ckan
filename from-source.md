# วิธีการติดตั้ง CKAN 2.9 จาก Source

### 1. Update Package ของ Ubuntu:
```sh
sudo apt-get update
```

### 2. ติดตั้ง Package ของ Ubuntu ที่ CKAN ต้องการ:
```sh
sudo apt-get install libpython2.7 libpq5 python-pip python-virtualenv git-core solr-jetty openjdk-8-jdk redis-server
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

### 4. ตั้งค่า CKAN path:
```sh
#เตรียม ckan path
sudo mkdir -p /usr/lib/ckan/default

sudo chown -R `whoami` /usr/lib/ckan/default

#เตรียม storage path
sudo mkdir -p /var/lib/ckan/default/storage

sudo chown -R www-data:www-data /var/lib/ckan && sudo chmod -R 775 /var/lib/ckan
```

### 5. ติดตั้ง CKAN:
```sh
virtualenv --no-site-packages /usr/lib/ckan/default

. /usr/lib/ckan/default/bin/activate

pip install setuptools==44.1.0

pip install --upgrade pip

pip install -e 'git+https://github.com/ckan/ckan.git@ckan-2.9.1#egg=ckan[requirements-py2]'

deactivate

. /usr/lib/ckan/default/bin/activate
```

### 6. ตั้งค่า PostgreSQL:
```sh
sudo apt-get install -y postgresql

# สร้าง postgres user สำหรับ ckan_default และใส่ ***{password1}***
sudo -u postgres createuser -S -D -R -P ckan_default

# สร้างฐานข้อมูล ckan_default
sudo -u postgres createdb -O ckan_default ckan_default -E utf-8

# สร้าง postgres user สำหรับ datastore_default และใส่ ***{password2}***
sudo -u postgres createuser -S -D -R -P -l datastore_default

# สร้างฐานข้อมูล datastore_default
sudo -u postgres createdb -O ckan_default datastore_default -E utf-8

#ตรวจสอบ database list ให้มี database ckan_default และ datastore_default
sudo -u postgres psql -l
```

### 7. ติดตั้งและตั้งค่า Solr:
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
sudo mv /etc/ckan/default/who.ini /etc/ckan/default/who.ini.bak

sudo ln -s /usr/lib/ckan/default/src/ckan/who.ini /etc/ckan/default/who.ini
```
#### 8.2 แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
sudo mkdir -p /etc/ckan/default

sudo chown -R `whoami` /etc/ckan/

ckan generate config /etc/ckan/default/ckan.ini

sudo vi /etc/ckan/default/ckan.ini
    - เพิ่มค่า config ถัดจาก [app:main] (มีอยู่แล้ว)
        [app:main]
        ckan.tracking_enabled = true
    - แก้ไข {password1} (จากการตั้งค่าในขั้นตอนที่ 4) ของ sqlalchemy.url
        > sqlalchemy.url = postgresql://ckan_default:{password1}@localhost/ckan_default
    - เปิดการใช้งาน และแก้ไข {password1} (จากการตั้งค่าในขั้นตอนที่ 4) ของ ckan.datastore.write_url
        > ckan.datastore.write_url = postgresql://ckan_default:{password1}@localhost/datastore_default
    - เปิดการใช้งาน และแก้ไข {password2} (จากการตั้งค่าในขั้นตอนที่ 4) ของ ckan.datastore.read_url
        > ckan.datastore.read_url = postgresql://datastore_default:{password2}@localhost/datastore_default
    - กำหนด ip ที่ ckan.site_url
        > ckan.site_url = http://{ip address}:5000
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

```

#### 8.3 เริ่มต้นสร้างฐานข้อมูลสำหรับ CKAN:
```sh
cd /usr/lib/ckan/default/src/ckan

ckan -c /etc/ckan/default/ckan.ini db init
```

### 9. cronjob สำหรับ page view tracking:
```sh
crontab -e
```
เพิ่มคำสั่งต่อไปนี้

    @hourly /usr/lib/ckan/default/bin/ckan -c /etc/ckan/default/ckan.ini tracking update && /usr/lib/ckan/default/bin/ckan -c /etc/ckan/default/ckan.ini search-index rebuild -r

### 10. ปรับแก้ไขสิทธิ์ที่จำเป็น:
```sh
sudo chmod -R 775 /usr/lib/ckan/default/src/ckan/ckan/public

sudo chown -R www-data:www-data /usr/lib/ckan/default/src/ckan/ckan/public
```

### 11. สร้าง CKAN SysAdmin และกำหนดสิทธิ์ DataStore:

```sh
#เปลี่ยน {username}
ckan -c /etc/ckan/default/ckan.ini sysadmin add {username}

paster --plugin=ckan datastore set-permissions -c /etc/ckan/default/production.ini | sudo -u postgres psql --set ON_ERROR_STOP=1

cd /usr/lib/ckan/default/src/ckan

ckan -c /etc/ckan/default/ckan.ini run
```

### 12. ทดสอบเรียกใช้เว็บไซต์ผ่าน http://{ip address}:5000 และ login ด้วย SysAdmin

### 13. ติดตั้งและตั้งค่า [CKAN Extensions](ckan-extension.md)

### 14. วิธีการ Deploy ckan 
#### ckan 2.8 
1. ติดตั้ง package ดังนี้
```sh
$ sudo apt-get install nginx apache2 libapache2-mod-wsgi libapache2-mod-rpaf
```
2. สร้างไฟล์ WSGI script
สร้างไฟล์ apache.wsgi ที่ /etc/ckan/default/apache.wsgi แล้ววางคำสั่งตามนี้
```sh
import os
activate_this = os.path.join('/usr/lib/ckan/default/bin/activate_this.py')
execfile(activate_this, dict(__file__=activate_this))

from paste.deploy import loadapp
config_filepath = os.path.join(os.path.dirname(os.path.abspath(__file__)), 'production.ini')
from paste.script.util.logging_config import fileConfig
fileConfig(config_filepath)
application = loadapp('config:%s' % config_filepath)
```
3. สร้างไฟล์ apache config เพื่อให้ wsgi script ทำงาน
โดยสร้างไฟล์ config ขึ้นมาดังนี้ /etc/apache2/sites-available/ckan_default.conf แล้วนำคำสั่งด้านล่างไปใส่
```sh
<VirtualHost 127.0.0.1:8080>
    ServerName default.ckanhosted.com
    ServerAlias www.default.ckanhosted.com
    WSGIScriptAlias / /etc/ckan/default/apache.wsgi

    # Pass authorization info on (needed for rest api).
    WSGIPassAuthorization On

    # Deploy as a daemon (avoids conflicts between CKAN instances).
    WSGIDaemonProcess ckan_default display-name=ckan_default processes=2 threads=15

    WSGIProcessGroup ckan_default

    ErrorLog /var/log/apache2/ckan_default.error.log
    CustomLog /var/log/apache2/ckan_default.custom.log combined

    <IfModule mod_rpaf.c>
        RPAFenable On
        RPAFsethostname On
        RPAFproxy_ips 127.0.0.1
    </IfModule>

    <Directory />
        Require all granted
    </Directory>

</VirtualHost>
```
4. เปลี่ยน port apache
ให้ทำการแก้ไขไฟล์ /etc/apache2/ports.conf ให้แก้ไขจาก
    > Listen 80

    เป็น
    > Listen 8080
5. สร้างไฟล์ config สำหรับ nginx 
ทำการสร้างไฟล์ config สำหรับ nginx ดังนี้ /etc/nginx/sites-available/ckan แล้ววางคำสั่งด้านล่างลงไป
```sh
proxy_cache_path /tmp/nginx_cache levels=1:2 keys_zone=cache:30m max_size=250m;
proxy_temp_path /tmp/nginx_proxy 1 2;

server {
    client_max_body_size 100M;
    location / {
        proxy_pass http://127.0.0.1:8080/;
        proxy_set_header X-Forwarded-For $remote_addr;
        proxy_set_header Host $host;
        proxy_cache cache;
        proxy_cache_bypass $cookie_auth_tkt;
        proxy_no_cache $cookie_auth_tkt;
        proxy_cache_valid 30m;
        proxy_cache_key $host$scheme$proxy_host$request_uri;
        # In emergency comment out line to force caching
        # proxy_ignore_headers X-Accel-Expires Expires Cache-Control;
    }

}
```
6. เริ่มต้นใช้งาน CKAN
ก่อนเริ่มต้นการใช้งาน CKAN บน server ให้ทำตามคำสั่งด้านล่างดังนี้
```sh
# เปิดใช้งานเว็บไซด์ ckan บน apache
$ sudo a2ensite ckan_default
# ปิดเว็บ default ของ apache
$ sudo a2dissite 000-default
# ลบไฟล์ default config ของ nginx ออก
$ sudo rm /etc/nginx/sites-available/default
# เปิดใช้งาน CKAN สำหรับ nginx
$ sudo ln -s /etc/nginx/sites-available/ckan /etc/nginx/sites-enabled/ckan_default
# รีสตาท apache  และ nginx
$ sudo service apache2 restart
$ sudo service nginx restart
```
7. ติดตั้ง worker ด้วย Supervisor
    - ติดตั้ง supervisor
    ```sh
    $ sudo apt-get install supervisor
    ```
    - สร้าง config สำหรับ supervisor
    ```sh
    $ sudo cp /usr/lib/ckan/default/src/ckan/ckan/config/supervisor-ckan-worker.conf /etc/supervisor/
    ```
    - รีสตาท supervisor 
    ```sh
    $ sudo service supervisor restart
    ```
    - ตรวจสอบสถานะของ supervisor
    ```sh
    $ sudo supervisorctl status
    ```
    - รีสตาท supervisor เฉพาะของ CKAN
    ```sh
    $ sudo supervisorctl restart ckan-worker:*
    ```
##### อ้างอิง CKAN 2.8
[Deploying a source install](https://docs.ckan.org/en/2.8/maintaining/installing/deployment.html)

[Background jobs](https://docs.ckan.org/en/2.8/maintaining/background-tasks.html#using-supervisor)

#### ckan 2.9
1. ติดตั้ง nginx 
```sh
$ sudo apt-get install nginx
```
2. สร้างไฟล์ script wsgi
```sh
sudo cp /usr/lib/ckan/default/src/ckan/wsgi.py /etc/ckan/default/
#####ตัวหย่างคำสั่งด้านในไฟล์ wsgi.py#####
# -- coding: utf-8 --

import os
from ckan.config.middleware import make_app
from ckan.cli import CKANConfigLoader
from logging.config import fileConfig as loggingFileConfig
config_filepath = os.path.join(
    os.path.dirname(os.path.abspath(__file__)), 'ckan.ini')
abspath = os.path.join(os.path.dirname(os.path.abspath(__file__)))
loggingFileConfig(config_filepath)
config = CKANConfigLoader(config_filepath).get_config()
application = make_app(config)

```
3. สร้าง wsgi server 
```sh
# เปิดการทำงาน virtualenv
/usr/lib/ckan/default/bin/activate
(default)$ pip install uwsgi
(default)$ sudo cp /usr/lib/ckan/default/src/ckan/ckan-uwsgi.ini /etc/ckan/default/
#####ตัวอย่างคำสั่งของ ckan-uwsgi.ini#####
[uwsgi]

http            =  127.0.0.1:8080
uid             =  www-data
guid            =  www-data
wsgi-file       =  /etc/ckan/default/wsgi.py
virtualenv      =  /usr/lib/ckan/default
module          =  wsgi:application
master          =  true
pidfile         =  /tmp/%n.pid
harakiri        =  50
max-requests    =  5000
vacuum          =  true
callable        =  application
```
4. ติดตั้ง supervisor สำหรับรัน uwsgi
```sh
sudo apt-get install supervisor
sudo service supervisor restart
```
5. สร้างไฟล์ config supervisor สำหรับ uwsgi 
สร้างไฟล์ /etc/supervisor/conf.d/ckan-uwsgi.conf นำคำสั่งด้านล่างไปวางในไฟล์ที่สร้าง
```sh
[program:ckan-uwsgi]

command=/usr/lib/ckan/default/bin/uwsgi -i /etc/ckan/default/ckan-uwsgi.ini

; Start just a single worker. Increase this number if you have many or
; particularly long running background jobs.
numprocs=1
process_name=%(program_name)s-%(process_num)02d

; Log files - change this to point to the existing CKAN log files
stdout_logfile=/etc/ckan/default/uwsgi.OUT
stderr_logfile=/etc/ckan/default/uwsgi.ERR

; Make sure that the worker is started on system start and automatically
; restarted if it crashes unexpectedly.
autostart=true
autorestart=true

; Number of seconds the process has to run before it is considered to have
; started successfully.
startsecs=10

; Need to wait for currently executing tasks to finish at shutdown.
; Increase this if you have very long running tasks.
stopwaitsecs = 600

; Required for uWSGI as it does not obey SIGTERM.
stopsignal=QUIT
```
6. สร้างไฟล์ config nginx
สร้างไฟลใหม่ขึ้นมาดังนี้ /etc/nginx/sites-available/ckan นำคำสั่งด้านล่างไปวางในไฟล์ที่สร้างขึ้นมา
```sh
proxy_cache_path /tmp/nginx_cache levels=1:2 keys_zone=cache:30m max_size=250m;
proxy_temp_path /tmp/nginx_proxy 1 2;

server {
    client_max_body_size 100M;
    location / {
        proxy_pass http://127.0.0.1:8080/;
        proxy_set_header X-Forwarded-For $remote_addr;
        proxy_set_header Host $host;
        proxy_cache cache;
        proxy_cache_bypass $cookie_auth_tkt;
        proxy_no_cache $cookie_auth_tkt;
        proxy_cache_valid 30m;
        proxy_cache_key $host$scheme$proxy_host$request_uri;
        # In emergency comment out line to force caching
        # proxy_ignore_headers X-Accel-Expires Expires Cache-Control;
    }

}
```
7. เริ่มการใช้งาน CKAN
```sh
# ลบไฟล์ default ของ nginx ออก
$ sudo rm -vi /etc/nginx/sites-enabled/default
# เปิดใช้งาน CKAN สำหรับ ngixn
$ sudo ln -s /etc/nginx/sites-available/ckan /etc/nginx/sites-enabled/ckan
# รีสตาท nginx
$ sudo service nginx restart
```
##### อ้างอิง CKAN 2.9
[Deploying a source install](https://docs.ckan.org/en/2.9/maintaining/installing/deployment.html)

[Background jobs](https://docs.ckan.org/en/2.9/maintaining/background-tasks.html#background-jobs)



