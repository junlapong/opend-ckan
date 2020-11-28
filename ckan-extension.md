### 1. ckanext-pdfview:
```sh
. /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default/src

sudo git clone https://github.com/ckan/ckanext-pdfview.git

cd /usr/lib/ckan/default/src/ckanext-pdfview

sudo python setup.py develop
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
sudo vi /etc/ckan/default/production.ini
```
```sh
    - ckan.plugins
        > ckan.plugins = stats text_view image_view recline_view resource_proxy datastore datapusher webpage_view pdf_view
    - ckan.views.default_views
        > ckan.views.default_views = image_view text_view recline_view webpage_view pdf_view
```
```sh
sudo service apache2 restart
```
### 2. ckanext-scheming:
```sh
. /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default/src

sudo git clone https://github.com/ckan/ckanext-scheming.git

cd /usr/lib/ckan/default/src/ckanext-scheming

sudo python setup.py develop

sudo pip install -r requirements.txt
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
sudo vi /etc/ckan/default/production.ini
```
```sh
    - ckan.plugins
        > ckan.plugins = stats text_view image_view recline_view resource_proxy datastore datapusher webpage_view pdf_view scheming_datasets
```
```sh
sudo service apache2 restart
```
### 3. ckanext-pages:
```sh
. /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default/src

sudo git clone https://github.com/ckan/ckanext-pages.git

cd /usr/lib/ckan/default/src/ckanext-pages

sudo python setup.py develop

sudo pip install -r dev-requirements.txt
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
sudo vi /etc/ckan/default/production.ini
```
```sh
    - เพิ่มค่า config ถัดจาก [app:main] (มีอยู่แล้ว)
        [app:main]
        ckan.auth.public_user_details = false
        ckan.tracking_enabled = true
        ckanext.pages.form = pages/base_form.html
        ckanext.pages.allow_html = True
        ckanext.pages.editor = ckeditor
    - ckan.plugins
        > ckan.plugins = stats text_view image_view recline_view resource_proxy datastore datapusher webpage_view pdf_view scheming_datasets pages
```
```sh
sudo service apache2 restart
```
### 4. ckanext-hierarchy:
```sh
. /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default/src

sudo git clone https://github.com/davidread/ckanext-hierarchy.git

cd /usr/lib/ckan/default/src/ckanext-hierarchy

sudo python setup.py develop

sudo pip install -r requirements.txt
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
sudo vi /etc/ckan/default/production.ini
```
```sh
    - ckan.plugins
        > ckan.plugins = stats text_view image_view recline_view resource_proxy datastore datapusher webpage_view pdf_view scheming_datasets pages hierarchy_display hierarchy_form
```
```sh
sudo service apache2 restart
```
### 5. ckanext-package-group-permissions:
```sh
. /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default/src

sudo git clone https://github.com/salsadigitalauorg/ckanext-package-group-permissions.git

cd /usr/lib/ckan/default/src/ckanext-package-group-permissions

sudo python setup.py develop

sudo pip install -r requirements.txt
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
sudo vi /etc/ckan/default/production.ini
```
```sh
    - ckan.plugins
        > ckan.plugins = stats text_view image_view recline_view resource_proxy datastore datapusher webpage_view pdf_view scheming_datasets pages hierarchy_display hierarchy_form package_group_permissions
```
```sh
sudo service apache2 restart
```
### 6. ckanext-dcat:
```sh
. /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default/src

sudo git clone https://github.com/ckan/ckanext-dcat.git

cd /usr/lib/ckan/default/src/ckanext-dcat

sudo python setup.py develop

sudo pip install -r requirements.txt
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
sudo vi /etc/ckan/default/production.ini
```
```sh
    - ckan.plugins
        > ckan.plugins = stats text_view image_view recline_view resource_proxy datastore datapusher webpage_view pdf_view scheming_datasets pages hierarchy_display hierarchy_form package_group_permissions dcat dcat_json_interface structured_data
```
```sh
sudo service apache2 restart
```
### 7. ckanext-thai_gdc:
```sh
. /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default/src

sudo git clone https://gitlab.nectec.or.th/opend/ckanext-thai_gdc.git

cd /usr/lib/ckan/default/src/ckanext-thai_gdc

sudo python setup.py develop
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
sudo vi /etc/ckan/default/production.ini
```
```sh
    - เพิ่มค่า config ถัดจาก [app:main] (มีอยู่แล้ว)
        [app:main]
        ckan.auth.public_user_details = false
        ckan.tracking_enabled = true
        ckanext.pages.form = pages/base_form.html
        ckanext.pages.allow_html = True
        ckanext.pages.editor = ckeditor
        scheming.dataset_schemas = ckanext.thai_gdc:ckan_dataset.json
    - ckan.plugins
        > ckan.plugins = stats text_view image_view recline_view resource_proxy datastore datapusher webpage_view pdf_view scheming_datasets pages hierarchy_display hierarchy_form package_group_permissions dcat dcat_json_interface structured_data thai_gdc
```
```sh
sudo service apache2 restart
```