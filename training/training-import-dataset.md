Source code: import CKAN Dataset
```sh
import requests
from ckanapi import RemoteCKAN
import io
import datetime

ckan_url = 'http://192.168.107.129'
package_name =  'dataset_80_01'
api_key = 'd4903c8c-8d60-482c-a64e-ab6940d1cbdd'

session = requests.Session()
session.verify = False
ua = 'ckanapiexample/1.0 (+http://example.com/my/website)'
portal = RemoteCKAN(ckan_url, user_agent=ua, apikey=api_key, session=session)
pkg_meta = {'name': package_name, 
            'title': 'ระบบการตรวจสอบย้อนกลับ แหล่งผลิตสินค้าหม่อนไหม ตามมาตรฐานสากล', 
            'owner_org': 'nectec', 
            'maintainer': 'กองตรวจสอบและรับรองมาตรฐานหม่อนไหม', 
            'maintainer_email': 'dss_silk@qsds.go.th', 
            'tag_string': ['ดวงตรานกยูงพระราชทาน', '2d barcode', 'การตรวจสอบย้อนกลับ'], 
            'notes': 'หมายเลขตรานกยูง มาตรฐานผ้าไหม\xa0ประเภทผ้า ลายผ้า ชื่อลาย ชนิดผ้า ชื่อผู้ผลิต ที่อยู่ วันที่ติดดวงตรา หมายเลขรับรอง  สถานที่ผลิต ชื่อพันธุ์ไหม', 
            'objective': 'แผนพัฒนาเศรษฐกิจและสังคมแห่งชาติ', 
            'update_frequency_unit': 'ตามเวลาจริง', 
            'geo_coverage': 'พิกัด', 
            'data_source': 'ผู้ขอการรับรองยื่นคำขอในระบบ', 
            'data_format': 'Database', 
            'data_category': 'ข้อมูลสาธารณะ', 
            'license_id': 'Open Data Common', 
            'accessible_condition': 'ต้องมีรหัสดวงตรา', 
            'url': 'https://qsds.go.th/traceability/', 
            'data_support': 'หน่วยงานของรัฐ', 
            'data_collect': 'บุคคล', 
            'data_language': 'ไทย', 
            'data_type': 'ข้อมูลระเบียน', 
            'dataset_name': 'Dataset_80_01', 
            'private': True}
package = portal.action.package_create(**pkg_meta)
print(package)
```
Source code: get Data from Dremio
```sh
from sqlalchemy import create_engine
import sqlalchemy.dialects
import pandas as pd
import numpy as np
import os
from pathlib import Path
import jaydebeapi


host = 'localhost'
port = 31010
uid = 'admin'
pwd = '***'
driver = "dremio-jdbc-driver-4.0.2-201910020123580864-a98a0b9.jar"

conn = jaydebeapi.connect("com.dremio.jdbc.Driver",
                           "jdbc:dremio:direct={}:{}".format(host, port),
                           [uid, pwd],
                           "dremio-jdbc-driver-4.0.2-201910020123580864-a98a0b9.jar",)

sql = "select * from myspace.final1"

df = pd.read_sql(sql, conn)
df
```
Source code: import Resource into Dataset
```sh

from ckanapi import RemoteCKAN
import requests, io, json, csv, datetime

session = requests.Session()
session.verify = False

api_key = 'd4903c8c-8d60-482c-a64e-ab6940d1cbdd'
package_name = 'api-test'

resource_name = 'จำนวนเกษตรกร'#res_meta_title
resource_desc = 'จำนวนเกษตรกร'#res_meta_des
ua = 'ckanapiexample/1.0 (+http://example.com/my/website)'
portal = RemoteCKAN('http://192.168.107.129', user_agent=ua, apikey=api_key, session=session)

s_buf = io.StringIO()
s_buf.name = 'groupagri.csv'

df.to_csv(s_buf, sep=',', encoding='utf-8', index=False)

resource_metadata = {
    'package_id'    : package_name,
    'name'          : resource_name,
    'description'   : resource_desc,
    'format'        : 'CSV',
    'mimetype' : 'text/csv',
    'upload'           : io.StringIO(s_buf.getvalue()),
    'created'       : datetime.datetime.now().isoformat(),
    'last_modified' : datetime.datetime.now().isoformat()
}

resource = portal.action.resource_create(**resource_metadata)
resource
```