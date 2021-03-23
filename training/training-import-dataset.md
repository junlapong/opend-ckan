## Source code: import CKAN Package
```sh
import requests
from ckanapi import RemoteCKAN
import io
import datetime

ckan_url = 'http://192.168.107.129' #CKAN URL
api_key = 'd4903c8c-8d60-482c-a64e-ab6940d1cbdd' #API-Key

session = requests.Session()
session.verify = False
ua = 'ckanapiexample/1.0 (+http://example.com/my/website)'
portal = RemoteCKAN(ckan_url, user_agent=ua, apikey=api_key, session=session)
pkg_meta = {'name': 'dataset_01', 
            'title': 'ชื่อชุดข้อมูล', 
            'owner_org': 'nectec', 
            'maintainer': 'หน่วยงานเจ้าของข้อมูล', 
            'maintainer_email': 'maintainer@mail.go.th', 
            'tag_string': ['แท็ก 1', 'แท็ก 2'], 
            'notes': 'รายละเอียดชุดข้อมูล', 
            'update_frequency_unit': 'ตามเวลาจริง', 
            'geo_coverage': 'พิกัด', 
            'license_id': 'Open Data Common', 
            'private': True}
package = portal.action.package_create(**pkg_meta)
package
```
## Source code: get Data from Dremio
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
pwd = '******'
driver = "dremio-jdbc-driver-4.0.2-201910020123580864-a98a0b9.jar"

conn = jaydebeapi.connect("com.dremio.jdbc.Driver",
                           "jdbc:dremio:direct={}:{}".format(host, port),
                           [uid, pwd],
                           "dremio-jdbc-driver-4.0.2-201910020123580864-a98a0b9.jar",)

sql = "select * from myspace.project1"

df = pd.read_sql(sql, conn)
df
```
## Source code: import Resource to Package
```sh

from ckanapi import RemoteCKAN
import requests, io, json, csv, datetime

#read file
#df = pd.read_csv('report_1.csv',sep='|',quoting=csv.QUOTE_ALL,encoding='utf-8')

session = requests.Session()
session.verify = False

ckan_url = 'http://164.115.20.130' #CKAN URL
api_key = '' #API-Key
package_name = '' #Package name or id

ua = 'ckanapiexample/1.0 (+http://example.com/my/website)'
portal = RemoteCKAN(ckan_url, user_agent=ua, apikey=api_key, session=session)

s_buf = io.StringIO()
s_buf.name = 'resourcefile.csv' #File name for buffer

df.to_csv(s_buf, sep=',', encoding='utf-8', index=False)

resource_metadata = {
    'package_id'    : package_name,
    'name'          : 'ชื่อทรัพยากร',
    'description'   : 'รายละเอียดทรัพยากร',
    'format'        : 'CSV',
    'mimetype' : 'text/csv',
    'upload'           : io.StringIO(s_buf.getvalue()),
    'created'       : datetime.datetime.now().isoformat(),
    'last_modified' : datetime.datetime.now().isoformat()
}

resource = portal.action.resource_create(**resource_metadata)
session.close()
resource
```

## Source code: Update Resource
```sh
session = requests.Session()
session.verify = False

ckan_url = 'http://164.115.20.130' #CKAN URL
api_key = '' #API-Key
resource_id = '' #Resource id

ua = 'ckanapiexample/1.0 (+http://example.com/my/website)'
portal = RemoteCKAN(ckan_url, user_agent=ua, apikey=api_key, session=session)

s_buf = io.StringIO()
s_buf.name = 'resourcefile_updated.csv' #File name

df.to_csv(s_buf, sep=',', encoding='utf-8', index=False)

resource_metadata = {
    'id'    : resource_id,
    'name'          : 'ชื่อทรัพยากรที่ปรับปรุง',
    'description'   : 'รายละเอียดทรัพยากร',
    'format'        : 'CSV',
    'mimetype' : 'text/csv',
    'upload'           : io.StringIO(s_buf.getvalue()),
    'created'       : datetime.datetime.now().isoformat(),
    'last_modified' : datetime.datetime.now().isoformat()
}

resource = portal.action.resource_update(**resource_metadata)
session.close()
resource
```