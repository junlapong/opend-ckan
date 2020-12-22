### 1. ckanext-pdfview:
```sh
source /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default

pip install -e 'git+https://gitlab.nectec.or.th/opend/ckanext-pdfview.git#egg=ckanext-pdfview'
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
#สำหรับ CKAN 2.9:
sudo vi /etc/ckan/default/ckan.ini
#สำหรับ CKAN 2.8:
sudo vi /etc/ckan/default/production.ini
```
```sh
    - ckan.plugins (เติม pdf_view ต่อจากที่มีอยู่แล้ว)
        > ckan.plugins = ... pdf_view
    - ckan.views.default_views (เติม pdf_view ต่อจากที่มีอยู่แล้ว)
        > ckan.views.default_views = ... pdf_view
```
```sh
#สำหรับ CKAN 2.9:
sudo supervisorctl reload
#สำหรับ CKAN 2.8:
sudo service apache2 restart
```
### 2. ckanext-scheming:
```sh
source /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default

pip install -e 'git+https://github.com/ckan/ckanext-scheming.git#egg=ckanext-scheming'

pip install -r src/ckanext-scheming/requirements.txt
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
#สำหรับ CKAN 2.9:
sudo vi /etc/ckan/default/ckan.ini
#สำหรับ CKAN 2.8:
sudo vi /etc/ckan/default/production.ini
```
```sh
    - ckan.plugins (เติม scheming_datasets ต่อจากที่มีอยู่แล้ว)
        > ckan.plugins = ... scheming_datasets
```
```sh
#สำหรับ CKAN 2.9:
sudo supervisorctl reload
#สำหรับ CKAN 2.8:
sudo service apache2 restart
```

### 3. ckanext-hierarchy:
```sh
source /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default

pip install -e 'git+https://github.com/davidread/ckanext-hierarchy.git#egg=ckanext-hierarchy'

pip install -r src/ckanext-hierarchy/requirements.txt
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
#สำหรับ CKAN 2.9:
sudo vi /etc/ckan/default/ckan.ini
#สำหรับ CKAN 2.8:
sudo vi /etc/ckan/default/production.ini
```
```sh
    - ckan.plugins (เติม hierarchy_display hierarchy_form ต่อจากที่มีอยู่แล้ว)
        > ckan.plugins = ... hierarchy_display hierarchy_form
```
```sh
#สำหรับ CKAN 2.9:
sudo supervisorctl reload
#สำหรับ CKAN 2.8:
sudo service apache2 restart
```
### 4. ckanext-dcat:
```sh
source /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default

pip install -e 'git+https://github.com/ckan/ckanext-dcat.git#egg=ckanext-dcat'

pip install -r src/ckanext-dcat/requirements.txt
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
#สำหรับ CKAN 2.9:
sudo vi /etc/ckan/default/ckan.ini
#สำหรับ CKAN 2.8:
sudo vi /etc/ckan/default/production.ini
```
```sh
    - ckan.plugins (เติม dcat dcat_json_interface structured_data ต่อจากที่มีอยู่แล้ว)
        > ckan.plugins = ... dcat dcat_json_interface structured_data
```
```sh
#สำหรับ CKAN 2.9:
sudo supervisorctl reload
#สำหรับ CKAN 2.8:
sudo service apache2 restart
```
### 5. ckanext-thai_gdc:
```sh
source /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default

pip install -e 'git+https://gitlab.nectec.or.th/opend/ckanext-thai_gdc.git#egg=ckanext-thai_gdc'
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
#สำหรับ CKAN 2.9:
sudo vi /etc/ckan/default/ckan.ini
#สำหรับ CKAN 2.8:
sudo vi /etc/ckan/default/production.ini
```
```sh
    - ckan.plugins (เติม thai_gdc ไว้ท้ายสุด)
        > ckan.plugins = ... thai_gdc
```
```sh
#สำหรับ CKAN 2.9:
sudo supervisorctl reload
#สำหรับ CKAN 2.8:
sudo service apache2 restart
```
