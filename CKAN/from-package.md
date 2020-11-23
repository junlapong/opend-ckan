# วิธีการติดตั้ง CKAN จาก Package บน Ubuntu Linux

การติดตั้ง CKAN จาก Package จะมี port และ service ที่ต้องใช้ดังนี้

| Service | Port | Used for |
| ------- | ---- | -------- |
| NGINX | 80 | Proxy |
| Apache2 | 8080 | Web Server |
| Solr/Jetty | 8983 | Search |
| PostgreSQL | 5432 | Database |
| Redis | 6379 | Search |

### 1. อัพเดท Package ของ Ubuntu:
```sh
sudo apt-get update
```

### 2. ตั้งค่า python2 และ pip2:
```sh
#ตรวจสอบเวอร์ชั่นของ python และตั้งค่า default python2
python -V
    # หากยังไม่ใช่ version 2 ใช้คำสั่งนี้ และ select python2
    sudo update-alternatives --config python

#ตรวจสอบเวอร์ชั่นของ pip และตั้งค่า default pip2
pip -V
    # หากยังไม่ใช่ version 2 ใช้คำสั่งนี้
    sudo cp /usr/local/bin/pip2 /usr/local/bin/pip
```

### 3. ติดตั้ง Package ของ Ubuntu ที่ CKAN ต้องการ:
```sh
sudo apt-get install -y nginx apache2 libapache2-mod-wsgi libpq5 redis-server git-core
```

### 4. ดาวน์โหลด CKAN Package ตามเวอร์ชั่นของ Ubuntu:
ตรวจสอบเวอร์ชั่นของ Ubuntu โดยใช้คำสั่ง cat /etc/os-release
- ดาวน์โหลด CKAN Package สำหรับ Ubuntu 20.04:
```sh
    wget http://packaging.ckan.org/python-ckan_2.9-py2-focal_amd64.deb
```
- ดาวน์โหลด CKAN Package สำหรับ Ubuntu 18.04:
```sh
    wget http://packaging.ckan.org/python-ckan_2.9-bionic_amd64.deb
```
- ดาวน์โหลด CKAN Package สำหรับ Ubuntu 16.04:
```sh
    wget http://packaging.ckan.org/python-ckan_2.8-xenial_amd64.deb
```

### 5. ติดตั้งและตั้งค่า PostgreSQL
```sh
sudo apt-get install -y postgresql

# สร้าง postgres user และใส่ password
sudo -u postgres createuser -S -D -R -P ckan_default

sudo -u postgres createdb -O ckan_default ckan_default -E utf-8

#ตรวจสอบ database list ให้ database ckan_default
sudo -u postgres psql -l
```

### 6. ตั้งค่า Apache และ Nginx:
```sh
#ตั้งค่า Apache
wget https://gitlab.nectec.or.th/opend/ckan-for-gdc/-/raw/master/CKAN/config/apache/ckan_default.conf -P ./apache

sudo cp ./apache/ckan_default.conf /etc/apache2/sites-available/ckan_default.conf

#ตั้งค่า Nginx
wget https://gitlab.nectec.or.th/opend/ckan-for-gdc/-/raw/master/CKAN/config/nginx/ckan_default.conf -P ./nginx

sudo cp ./nginx/ckan_default.conf /etc/nginx/conf.d/ckan_default.conf

#เตรียม proxycache
sudo mkdir -p /var/cache/nginx/proxycache

sudo chown www-data /var/cache/nginx/proxycache
```

### 7. ติดตั้ง CKAN package:
- สำหรับ Ubuntu 20.04:
```sh
    sudo dpkg -i python-ckan_2.9-py2-focal_amd64.deb
```
- สำหรับ Ubuntu 18.04:
```sh
    sudo dpkg -i python-ckan_2.9-bionic_amd64.deb
```
- สำหรับ Ubuntu 16.04:
```sh
    sudo dpkg -i python-ckan_2.8-xenial_amd64.deb
```

### 8. ตั้งค่าและสร้างฐานข้อมูลสำหรับ CKAN
1. ตั้งค่า who.ini และ apache.wsgi:
```sh
#ตั้งค่า who.ini
sudo rm -rf /etc/ckan/default/who.ini

sudo ln -s /usr/lib/ckan/default/src/ckan/who.ini /etc/ckan/default/who.ini

#ตั้งค่า apache.wsgi
wget https://gitlab.nectec.or.th/thepaeth/CKAN-installation/-/raw/master/config/apache/apache.wsgi -P ./apache

sudo cp ./apache/apache.wsgi /etc/ckan/default/apache.wsgi
```

2. แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
sudo vi /etc/ckan/default/production.ini
    - sqlalchemy.url
        > sqlalchemy.url = postgresql://ckan_default:{password}@localhost/ckan_default
    - ckan.site_url
        > ckan.site_url = http://{domain name}

sudo service apache2 restart
```

3. เริ่มต้นสร้างฐานข้อมูลสำหรับ CKAN:
```sh
sudo ckan db init
```

### 9. ติดตั้งและตั้งค่า Solr
```sh
sudo apt-get install -y solr-jetty

sudo vi /etc/default/jetty8
    NO_START=0            # (line 4)
    JETTY_HOST=127.0.0.1  # (line 16)
    JETTY_PORT=8983       # (line 19)

sudo rm -rf /etc/solr/conf/schema.xml

sudo ln -s /usr/lib/ckan/default/src/ckan/ckan/config/solr/schema.xml /etc/solr/conf/schema.xml

sudo service jetty8 restart
```

### 10. Restart Apache และ Nginx
```sh
sudo rm -rf /etc/nginx/sites-enabled/ckan

sudo service apache2 restart

sudo service nginx restart
```

### 11. ทดสอบเรียกใช้เว็บไซต์ผ่าน http://{domain name}

### 12. ติดตั้งและตั้งค่า >> [CKAN Extension](packages/) 


