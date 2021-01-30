### ติดตั้ง CKAN Extensions
```sh
source /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default

#ckanext-pdfview
pip install -e 'git+https://gitlab.nectec.or.th/opend/ckanext-pdfview.git#egg=ckanext-pdfview'

#ckanext-scheming
pip install -e 'git+https://gitlab.nectec.or.th/opend/ckanext-scheming.git#egg=ckanext-scheming'

pip install -r src/ckanext-scheming/requirements.txt

#ckanext-hierarchy
pip install -e 'git+https://github.com/ckan/ckanext-hierarchy.git#egg=ckanext-hierarchy'

pip install -r src/ckanext-hierarchy/requirements.txt

#ckanext-dcat
pip install -e 'git+https://github.com/ckan/ckanext-dcat.git#egg=ckanext-dcat'

pip install -r src/ckanext-dcat/requirements.txt

#ckanext-thai_gdc
pip install -e 'git+https://gitlab.nectec.or.th/opend/ckanext-thai_gdc.git#egg=ckanext-thai_gdc'

#ckanext-xloader
pip install -e 'git+https://gitlab.nectec.or.th/opend/ckanext-xloader.git#egg=ckanext-xloader'

pip install -r src/ckanext-xloader/requirements.txt

pip install -U requests[security]

deactivate
```

แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
sudo vi /etc/ckan/default/ckan.ini
```
```sh
    - ckan.plugins (แทน datapusher ด้วย xloader และ extension ที่เหลือเติมต่อจากที่มีอยู่แล้ว)
        > ckan.plugins = ... xloader ... pdf_view scheming_datasets hierarchy_display hierarchy_form dcat dcat_json_interface structured_data thai_gdc
    - ckan.views.default_views (เติม pdf_view ต่อจากที่มีอยู่แล้ว)
        > ckan.views.default_views = ... pdf_view
    - ckanext.xloader.jobs_db.uri (เพิ่ม config นี้ ถัดจาก sqlalchemy.url และให้มีค่าเหมือนกัน)
        > ckanext.xloader.jobs_db.uri = postgresql://ckan_default:{password1}@localhost/ckan_default
```
```sh
sudo supervisorctl reload
```
