# วิธีการติดตั้ง CKAN 2.9 จาก Source

### 1. Update Package ของ Ubuntu:
```sh
sudo apt-get update
```

### 2. ติดตั้ง Package ของ Ubuntu ที่ CKAN ต้องการ:
```sh
sudo apt-get install python-dev libpq-dev redis-server python-pip python-virtualenv git-core
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

### 4. ตั้งค่า PostgreSQL:
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

### 5. ตั้งค่า CKAN path:
```sh
#เตรียม ckan path
sudo mkdir -p /usr/lib/ckan/default

sudo chown -R `whoami` /usr/lib/ckan/default

#เตรียม storage path
sudo mkdir -p /var/lib/ckan/default/storage

sudo chown -R `whoami` /var/lib/ckan && sudo chmod -R 775 /var/lib/ckan
```

### 6. ติดตั้ง CKAN:
```sh
virtualenv --no-site-packages /usr/lib/ckan/default

. /usr/lib/ckan/default/bin/activate

pip install setuptools==44.1.0

pip install --upgrade pip

pip install -e 'git+https://github.com/ckan/ckan.git@ckan-2.9.1#egg=ckan[requirements-py2]'

deactivate

. /usr/lib/ckan/default/bin/activate
```

### 7. ติดตั้งและตั้งค่า Solr:
```sh
sudo apt-get install openjdk-8-jdk

# ใช้คำสั่งนี้เพื่อให้เปลี่ยนไปใช้ openjdk-8-jdk
sudo update-alternatives --set java /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java

wget http://archive.apache.org/dist/lucene/solr/6.5.1/solr-6.5.1.tgz

tar xzf solr-6.5.1.tgz solr-6.5.1/bin/install_solr_service.sh --strip-components=2

sudo bash ./install_solr_service.sh solr-6.5.1.tgz

sudo su solr

cd /opt/solr/bin

./solr create -c ckan

cd /var/solr/data/ckan/conf

sed -i '/<config>/a <schemaFactory class="ClassicIndexSchemaFactory"/>' solrconfig.xml

sed -i '/<initParams path="\/update\/\*\*">/,/<\/initParams>/ s/.*/<!--&-->/' solrconfig.xml

sed -i '/<processor class="solr.AddSchemaFieldsUpdateProcessorFactory">/,/<\/processor>/ s/.*/<!--&-->/' solrconfig.xml

rm managed-schema

ln -s /usr/lib/ckan/default/src/ckan/ckan/config/solr/schema.xml schema.xml

exit

sudo service solr restart
```

### 8. ตั้งค่าและสร้างฐานข้อมูลสำหรับ CKAN
#### 8.1 ตั้งค่า who.ini:
```sh
sudo mkdir -p /etc/ckan/default

sudo ln -s /usr/lib/ckan/default/src/ckan/who.ini /etc/ckan/default/who.ini

sudo chown -R `whoami` /etc/ckan/
```
#### 8.2 แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
. /usr/lib/ckan/default/bin/activate

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
    - กำหนด ckan.site_url
        > ckan.site_url = http://localhost:5000
    - แก้ไข ckan.auth.user_delete_groups
        > ckan.auth.user_delete_groups = false
    - แก้ไข ckan.auth.user_delete_organizations
        > ckan.auth.user_delete_organizations = false
    - แก้ไข ckan.auth.public_user_details
        > ckan.auth.public_user_details = false
    - เปิดการใช้งาน และแก้ไข solr_url
        > solr_url = http://127.0.0.1:8983/solr/ckan
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
sudo chown -R `whoami` /usr/lib/ckan/default/src/ckan/ckan/public

sudo chmod -R 775 /usr/lib/ckan/default/src/ckan/ckan/public
```

### 11. สร้าง CKAN SysAdmin และกำหนดสิทธิ์ DataStore:

```sh
#เปลี่ยน {username}
ckan -c /etc/ckan/default/ckan.ini sysadmin add {username}

ckan -c /etc/ckan/default/ckan.ini datastore set-permissions | sudo -u postgres psql --set ON_ERROR_STOP=1

cd /usr/lib/ckan/default/src/ckan

ckan -c /etc/ckan/default/ckan.ini run
```

### 12. ทดสอบเรียกใช้เว็บไซต์ผ่าน http://localhost:5000 และ login ด้วย SysAdmin

### 13. วิธีการ set CKAN Production 
#### 13.1 ติดตั้ง nginx 
```sh
sudo apt-get install nginx
```
#### 13.2 สร้างไฟล์ script wsgi
```sh
sudo cp /usr/lib/ckan/default/src/ckan/wsgi.py /etc/ckan/default/
```
#### 13.3 สร้าง wsgi server 
```sh
. /usr/lib/ckan/default/bin/activate

pip install uwsgi

sudo cp /usr/lib/ckan/default/src/ckan/ckan-uwsgi.ini /etc/ckan/default/
```
#### 13.4 ติดตั้ง supervisor สำหรับรัน uwsgi
```sh
sudo apt-get install supervisor
```
#### 13.5 สร้างไฟล์ config supervisor สำหรับ uwsgi
```sh
# สร้าง log โฟร์เดอร์สำหรับ CKAN
sudo mkdir -p /var/log/ckan

# สร้าง supervisor config สำหรับ ckan-uwsgi
sudo vi /etc/supervisor/conf.d/ckan-uwsgi.conf
```
เพิ่มคำสั่งต่อไปนี้
```sh
[program:ckan-uwsgi]

command=/usr/lib/ckan/default/bin/uwsgi -i /etc/ckan/default/ckan-uwsgi.ini

; Start just a single worker. Increase this number if you have many or
; particularly long running background jobs.
numprocs=1
process_name=%(program_name)s-%(process_num)02d

; Log files - change this to point to the existing CKAN log files
stdout_logfile=/var/log/ckan/ckan-uwsgi.stdout.log
stderr_logfile=/var/log/ckan/ckan-uwsgi.stderr.log

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
#### 13.6 สร้างไฟล์ config nginx
```sh
sudo vi /etc/nginx/sites-available/ckan
```
เพิ่มคำสั่งต่อไปนี้
```sh
proxy_cache_path /var/cache/nginx/proxycache levels=1:2 keys_zone=cache:30m max_size=250m;
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
#### 13.7 เริ่มการใช้งาน CKAN
```sh
# ลบไฟล์ default ของ nginx ออก
sudo rm -r /etc/nginx/sites-enabled/default

# เปิดใช้งาน CKAN สำหรับ ngixn
sudo ln -s /etc/nginx/sites-available/ckan /etc/nginx/sites-enabled/ckan

#เตรียม proxycache
sudo mkdir -p /var/cache/nginx/proxycache && sudo chown www-data /var/cache/nginx/proxycache

#ปรับแก้ไขสิทธิ์ที่จำเป็น
sudo chown -R www-data:www-data /var/lib/ckan

sudo chown -R www-data:www-data /usr/lib/ckan/default/src/ckan/ckan/public

sudo chown -R www-data /tmp/default/

#แก้ไข CKAN config
sudo vi /etc/ckan/default/ckan.ini
    - กำหนด ip ที่ ckan.site_url
        > ckan.site_url = http://{ip address}


# รีสตาท Service
sudo service nginx restart

sudo supervisorctl reload
```

### 14. ทดสอบเรียกใช้เว็บไซต์ผ่าน http://{ip address}

### 15. ติดตั้งและตั้งค่า DataPusher
```sh
sudo apt-get install python-dev python-virtualenv build-essential libxslt1-dev libxml2-dev git libffi-dev

sudo virtualenv /usr/lib/ckan/datapusher

sudo mkdir /usr/lib/ckan/datapusher/src

cd /usr/lib/ckan/datapusher/src

sudo git clone https://gitlab.nectec.or.th/opend/datapusher.git

cd /usr/lib/ckan/datapusher/src/datapusher

sudo /usr/lib/ckan/datapusher/bin/pip install --use-feature=2020-resolver -r requirements.txt

sudo /usr/lib/ckan/datapusher/bin/python setup.py develop

sudo /usr/lib/ckan/datapusher/bin/pip install uwsgi

sudo cp -r /usr/lib/ckan/datapusher/src/datapusher/deployment /etc/ckan/datapusher

sudo chown `whoami` /etc/ckan/datapusher
```

สร้างไฟล์ supervisor config สำหรับ datapusher 
```sh
sudo vi /etc/supervisor/conf.d/ckan-datapusher.conf
```
เพิ่มคำสั่งต่อไปนี้
```sh
[program:ckan-datapusher]

command=/usr/lib/ckan/default/bin/uwsgi -i /etc/ckan/datapusher/datapusher-uwsgi.ini

; Start just a single worker. Increase this number if you have many or
; particularly long running background jobs.
numprocs=1
process_name=%(program_name)s-%(process_num)02d

; Log files
stdout_logfile=/var/log/ckan/ckan-datapusher.stdout.log
stderr_logfile=/var/log/ckan/ckan-datapusher.stderr.log

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

ทำการ reload supervisor
```sh
sudo supervisorctl reload
```
### 16. ติดตั้งและตั้งค่า [CKAN Extensions](ckan-extension.md)



