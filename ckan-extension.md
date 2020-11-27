### 1. ckanext-pdfview:
```sh
source /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default

sudo pip install -e 'git+https://github.com/ckan/ckanext-pdfview.git#egg=ckanext-pdfview'
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
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
source /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default

sudo pip install -e 'git+https://github.com/ckan/ckanext-scheming.git#egg=ckanext-scheming'

sudo pip install -r src/ckanext-scheming/requirements.txt
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
    - ckan.plugins
        > ckan.plugins = stats text_view image_view recline_view resource_proxy datastore datapusher webpage_view pdf_view scheming_datasets
```
```sh
sudo service apache2 restart
```
### 3. ckanext-pages:
```sh
source /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default

sudo pip install -e 'git+https://github.com/ckan/ckanext-pages.git#egg=ckanext-pages'

sudo pip install -r src/ckanext-pages/dev-requirements.txt
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
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
source /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default

sudo pip install -e 'git+https://github.com/davidread/ckanext-hierarchy.git#egg=ckanext-hierarchy'

sudo pip install -r src/ckanext-hierarchy/requirements.txt
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
    - ckan.plugins
        > ckan.plugins = stats text_view image_view recline_view resource_proxy datastore datapusher webpage_view pdf_view scheming_datasets pages hierarchy_display hierarchy_form
```
```sh
sudo service apache2 restart
```
### 5. ckanext-package-group-permissions:
```sh
source /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default

sudo pip install -e 'git+https://github.com/salsadigitalauorg/ckanext-package-group-permissions.git#egg=ckanext-package-group-permissions'

sudo pip install -r src/ckanext-package-group-permissions/requirements.txt
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
    - ckan.plugins
        > ckan.plugins = stats text_view image_view recline_view resource_proxy datastore datapusher webpage_view pdf_view scheming_datasets pages hierarchy_display hierarchy_form package_group_permissions
```
```sh
sudo service apache2 restart
```
### 6. ckanext-dcat:
```sh
source /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default

sudo pip install -e 'git+https://github.com/ckan/ckanext-dcat.git#egg=ckanext-dcat'

sudo pip install -r src/ckanext-dcat/requirements.txt
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
```sh
    - ckan.plugins
        > ckan.plugins = stats text_view image_view recline_view resource_proxy datastore datapusher webpage_view pdf_view scheming_datasets pages hierarchy_display hierarchy_form package_group_permissions dcat dcat_json_interface structured_data
```
```sh
sudo service apache2 restart
```
### 7. ckanext-thai_gdc:
```sh
source /usr/lib/ckan/default/bin/activate

cd /usr/lib/ckan/default

sudo pip install -e 'git+https://gitlab.nectec.or.th/opend/ckanext-thai_gdc.git#egg=ckanext-thai_gdc'
```
แก้ไขไฟล์ config ของ CKAN ดังนี้:
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