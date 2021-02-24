# วิธีการติดตั้ง CKAN

การติดตั้ง CKAN 2.9 (อ้างอิงจาก [ckan.org][docs]) มีวิธีการติดตั้งที่แนะนำดังนี้
   1. ติดตั้งจาก [Source][insSource]
   2. [แนะนำ] ติดตั้ง CKAN จาก [Package][insPackage2.9]
   3. ติดตั้งจาก [Docker Compose][insDocker]

CKAN Extension ที่ใช้สำหรับการติดตั้ง ระบบบัญชีข้อมูลหน่วยงาน (Agency Data Catalog)
   1. [ckanext-pdfview](ckan-extension.md#1-ckanext-pdfview)
   2. [ckanext-scheming](ckan-extension.md#2-ckanext-scheming)
   3. [ckanext-hierarchy](ckan-extension.md#3-ckanext-hierarchy)
   4. [ckanext-dcat](ckan-extension.md#4-ckanext-dcat)
   5. [ckanext-thai_gdc](ckan-extension.md#5-ckanext-thai_gdc)
   6. [ckanext-xloader](ckan-extension.md#6-ckanext-xloader)
   7. [ckanext-dga-stats](ckan-extension.md#6-ckanext-dga-stats)


# การพัฒนา CKAN extension เพื่อสนับสนุนการจัดทำ Government Data Catalog (ckanext-thai_gdc)

CKAN Extension เพื่อให้หน่วยงานภาครัฐของไทยนำไปติดตั้งเพื่อสร้าง "ระบบบัญชีข้อมูลหน่วยงาน (Agency Data Catalog)" ตามโครงการศึกษาและพัฒนาต้นแบบระบบบัญชีข้อมูลกลางภาครัฐ (Government Data Catalog) และระบบนามานุกรม (Directory Service) โดยความร่วมมือของสำนักงานสถิติแห่งชาติ (สสช.) สำนักงานพัฒนารัฐบาลดิจิทัล (องค์การมหาชน) (สพร.) สถาบันส่งเสริมการวิเคราะห์และบริหารข้อมูลขนาดใหญ่ภาครัฐ (สวข.) สถาบันเทคโนโลยีพระจอมเกล้าเจ้าคุณทหารลาดกระบัง และศูนย์เทคโนโลยีอิเล็กทรอนิกส์และคอมพิวเตอร์แห่งชาติ 

โดย extension มีคุณสมบัติทางเทคนิค ดังนี้

- รองรับการ Tag และ Search ภาษาไทย
- เมทาดาตา (metadata) เป็นไปตามมาตรฐานคำอธิบายข้อมูลหรือเมทาดาตาสำหรับชุดข้อมูลภาครัฐ ที่กำหนดโดยสำนักงานพัฒนารัฐบาลดิจิทัล (องค์การมหาชน) ร่วมกับสำนักงานสถิติแห่งชาติ และสถาบันส่งเสริมการวิเคราะห์และบริหารข้อมูลขนาดใหญ่ภาครัฐ
- รองรับการสร้าง Dataset ที่ไม่จำเป็นต้องมี Resource โดยไม่ติดสถานะ draft
- อนุญาตให้ผู้ใช้ที่เป็น editor สามารถกำหนด group ให้กับ dataset ได้
- รองรับการตั้งค่ารายละเอียดเว็บไซต์ที่จำเป็นสำหรับ Sysadmin เช่น banner footer ผ่านหน้า UI
- แสดงสถิติจำนวนผู้เข้าชมสำหรับ Dataset และสถิติการดาวน์โหลดสำหรับ Resource
- รองรับการเชื่อมโยง Catalog (Harvesting) กับระบบบัญชีข้อมูลกลางภาครัฐ (Government Data Catalog)
- รองรับการทำ data visualization ชุดข้อมูลเปิด โดยผ่านแพลตฟอร์ม Open-D

------------
Requirements
------------

สามารถติดตั้งร่วมกับ CKAN 2.8 ขึ้นไป โดยจำเป็นต้องติดตั้ง Extensions เหล่านี้ก่อน 

- https://github.com/ckan/ckanext-scheming
- https://github.com/davidread/ckanext-hierarchy


------------
Installation
------------

- [การติดตั้ง ckanext-thai_gdc](ckan-extension.md#5-ckanext-thai_gdc)



   [docs]: <https://docs.ckan.org/en/2.9/maintaining/installing/index.html>
   [insPackage2.9]: <from-package-2.9.md>
   [insSource]: <from-source.md>
   [insDocker]: <from-docker-compose.md>