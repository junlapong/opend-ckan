### 1. ckanext-pdfview:
```sh
source /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default

pip install -e 'git+https://gitlab.nectec.or.th/opend/ckanext-pdfview.git#egg=ckanext-pdfview'
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
sudo vi /etc/ckan/default/ckan.ini
```
```sh
    - ckan.plugins (เติม pdf_view ต่อจากที่มีอยู่แล้ว)
        > ckan.plugins = ... pdf_view
    - ckan.views.default_views (เติม pdf_view ต่อจากที่มีอยู่แล้ว)
        > ckan.views.default_views = ... pdf_view
```
```sh
sudo supervisorctl reload
```
### 2. ckanext-scheming:
```sh
source /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default

pip install -e 'git+https://gitlab.nectec.or.th/opend/ckanext-scheming.git#egg=ckanext-scheming'

pip install -r src/ckanext-scheming/requirements.txt
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
sudo vi /etc/ckan/default/ckan.ini
```
```sh
    - ckan.plugins (เติม scheming_datasets ต่อจากที่มีอยู่แล้ว)
        > ckan.plugins = ... scheming_datasets
```
```sh
sudo supervisorctl reload
```

### 3. ckanext-hierarchy:
```sh
source /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default

pip install -e 'git+https://gitlab.nectec.or.th/opend/ckanext-hierarchy.git#egg=ckanext-hierarchy'

pip install -r src/ckanext-hierarchy/requirements.txt
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
sudo vi /etc/ckan/default/ckan.ini
```
```sh
    - ckan.plugins (เติม hierarchy_display hierarchy_form ต่อจากที่มีอยู่แล้ว)
        > ckan.plugins = ... hierarchy_display hierarchy_form
```
```sh
sudo supervisorctl reload
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
sudo vi /etc/ckan/default/ckan.ini
```
```sh
    - ckan.plugins (เติม dcat dcat_json_interface structured_data ต่อจากที่มีอยู่แล้ว)
        > ckan.plugins = ... dcat dcat_json_interface structured_data
```
```sh
sudo supervisorctl reload
```
### 5. ckanext-thai_gdc:
```sh
source /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default

pip install -e 'git+https://gitlab.nectec.or.th/opend/ckanext-thai_gdc.git#egg=ckanext-thai_gdc'

pip install -r src/ckanext-thai-gdc/requirements.txt
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
sudo vi /etc/ckan/default/ckan.ini
```
```sh
    - เพิ่ม config ถัดจากบรรทัด [app:main]
        > scheming.dataset_schemas = ckanext.thai_gdc:ckan_dataset.json
    - ckan.plugins (เติม thai_gdc ไว้ท้ายสุด)
        > ckan.plugins = ... thai_gdc
```
```sh
sudo supervisorctl reload
```

#### การปรับปรุง extension
ก่อนการปรับปรุงควรตรวจสอบการ update ของ extension ckanext-scheming และ ckanext-hierarchy ก่อน

```sh
#ทำการ backup folder extension ก่อน
mv /usr/lib/ckan/default/src/ckanext-thai-gdc /usr/lib/ckan/default/src/ckanext-thai-gdc_bak 

#ติดตั้งตามขั้นตอนปกติของ extension
source /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default

pip install -e 'git+https://gitlab.nectec.or.th/opend/ckanext-thai_gdc.git#egg=ckanext-thai_gdc'

pip install -r src/ckanext-thai-gdc/requirements.txt

sudo supervisorctl reload
```
### 6. ckanext-xloader:
```sh
source /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default

pip install -e 'git+https://gitlab.nectec.or.th/opend/ckanext-xloader.git#egg=ckanext-xloader'

pip install -r src/ckanext-xloader/requirements.txt

pip install -U requests[security]
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
sudo vi /etc/ckan/default/ckan.ini
```
```sh
    - เพิ่ม config ถัดจากบรรทัด [app:main]
        > ckanext.xloader.just_load_with_messytables = true
        > ckanext.xloader.ssl_verify = false
    - ckan.plugins (เปลี่ยนจาก datapusher เป็น xloader)
        > ckan.plugins = ... xloader ...
    - ckanext.xloader.jobs_db.uri (เพิ่ม config นี้ ถัดจาก sqlalchemy.url และให้มีค่าเหมือนกัน)
        > ckanext.xloader.jobs_db.uri = postgresql://ckan_default:{password1}@localhost/ckan_default
```
```sh
sudo supervisorctl reload
```
หากต้องการกำหนดให้ xloader submit all อัตโนมัติเข้า DataStore ทุกวัน ให้ set cronjob ดังนี้
```sh
crontab -e
```
เพิ่มคำสั่งต่อไปนี้

    @daily /usr/lib/ckan/default/bin/paster --plugin=ckanext-xloader xloader submit all -c /etc/ckan/default/ckan.ini

### 7. ckanext-dga-stats:
```sh
source /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default

pip install -e 'git+https://gitlab.nectec.or.th/opend/ckanext-dga-stats.git#egg=ckanext-dga-stats'
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
sudo vi /etc/ckan/default/ckan.ini
```
```sh
    - ckan.plugins (เติม dga_stats ก่อนหน้า thai_gdc)
        > ckan.plugins = ... dga_stats ...
```
```sh
sudo supervisorctl reload
```