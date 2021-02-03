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