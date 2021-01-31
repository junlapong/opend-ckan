## 1. การปรับแต่ง Metadata ของ ชุดข้อมูล (1)
### 1.1 เพิ่มฟิลด์แบบ text ของ process และ subprocess
เปิดไฟล์ /usr/lib/ckan/default/src/ckanext-thai-gdc/ckanext/thai_gdc/ckan_dataset.json แทรกโค้ดนี้
```sh
      {
        "field_name": "process",
        "label": {
          "en": "Process",
          "th": "กระบวนการทำงานหลัก"
        },
        "required": true
      },
      {
        "field_name": "subprocess",
        "label": {
          "en": "Sub-Process",
          "th": "กระบวนการทำงานย่อย"
        }
      },
```
### 1.2 ฟิลด์แบบ select ของ process และ subprocess
เปิดไฟล์ /usr/lib/ckan/default/src/ckanext-thai-gdc/ckanext/thai_gdc/ckan_dataset.json แก้ไขฟิลด์ process และ subprocess ให้เป็นตามนี้
```sh
      {
        "field_name": "process",
        "label": {
          "en": "Process",
          "th": "กระบวนการทำงานหลัก"
        },
        "choices": [
          {
            "value": "กระบวนการที่ 1: ยื่นคำขอ",
            "label": "กระบวนการที่ 1: ยื่นคำขอ"
          },
          {
            "value": "กระบวนการที่ 2: ตรวจประเมินสถานที่ผลิตเครื่องมืออุปกรณ์",
            "label": "กระบวนการที่ 2: ตรวจประเมินสถานที่ผลิตเครื่องมืออุปกรณ์"
          },
          {
            "value": "กระบวนการที่ 3: ตรวจประเมินคุณภาพสินค้า",
            "label": "กระบวนการที่ 3: ตรวจประเมินคุณภาพสินค้า"
          }
        ],
        "preset": "select",
        "required": true
      },
      {
        "field_name": "subprocess",
        "label": {
          "en": "Sub-Process",
          "th": "กระบวนการทำงานย่อย"
        },
        "choices": [
          {
            "value": "กระบวนการที่ 1-1: ผู้ผลิต/ผู้รับมอบอำนาจ ยื่นคำขอการรับรอง",
            "label": "กระบวนการที่ 1-1: ผู้ผลิต/ผู้รับมอบอำนาจ ยื่นคำขอการรับรอง"
          },
          {
            "value": "กระบวนการที่ 2-1: ตรวจประเมินสถานที่ผลิต",
            "label": "กระบวนการที่ 2-1: ตรวจประเมินสถานที่ผลิต"
          },
          {
            "value": "กระบวนการที่ 2-2: ตรวจเครื่องมืออุปกรณ์ฯ",
            "label": "กระบวนการที่ 2-2: ตรวจเครื่องมืออุปกรณ์ฯ"
          },
          {
            "value": "กระบวนการที่ 2-3: ตรวจวัตถุดิบ",
            "label": "กระบวนการที่ 2-3: ตรวจวัตถุดิบ"
          },
          {
            "value": "กระบวนการที่ 3-1: ตรวจคุณภาพทั่วไป",
            "label": "กระบวนการที่ 3-1: ตรวจคุณภาพทั่วไป"
          },
          {
            "value": "กระบวนการที่ 3-2: ทดสอบทางห้องปฏิบัติการ",
            "label": "กระบวนการที่ 3-2: ทดสอบทางห้องปฏิบัติการ"
          }
        ],
        "form_snippet": "process.html",
        "display_snippet": "select.html"
      }
```
### 1.3 แก้ไข ฟิลด์ process ให้ทำงานตาม template html ที่กำหนด
เปิดไฟล์ /usr/lib/ckan/default/src/ckanext-thai-gdc/ckanext/thai_gdc/ckan_dataset.json แก้ไขฟิลด์ process ให้เป็นตามนี้
```sh
{
        "field_name": "process",
        "label": {
          "en": "Process",
          "th": "กระบวนการทำงานหลัก"
        },
        "choices": [
          {
            "value": "กระบวนการที่ 1: ยื่นคำขอ",
            "label": "กระบวนการที่ 1: ยื่นคำขอ"
          },
          {
            "value": "กระบวนการที่ 2: ตรวจประเมินสถานที่ผลิตเครื่องมืออุปกรณ์",
            "label": "กระบวนการที่ 2: ตรวจประเมินสถานที่ผลิตเครื่องมืออุปกรณ์"
          },
          {
            "value": "กระบวนการที่ 3: ตรวจประเมินคุณภาพสินค้า",
            "label": "กระบวนการที่ 3: ตรวจประเมินคุณภาพสินค้า"
          }
        ],
        "form_snippet": "process.html",
        "display_snippet": "select.html",
        "validators": "scheming_required",
        "required": true
      }
```
สร้างไฟล์ /usr/lib/ckan/default/src/ckanext-thai-gdc/ckanext/thai_gdc/templates/scheming/form_snippets/process.html
```sh
{% import 'macros/form.html' as form %}

{%- set options=[] -%}
{%- set form_restrict_choices_to=field.get('form_restrict_choices_to') -%}
{%- if not h.scheming_field_required(field) or
  field.get('form_include_blank_choice', false) -%}
{%- do options.append({'value': '', 'text': ''}) -%}
{%- endif -%}
{%- for c in h.scheming_field_choices(field) -%}
  {%- if not form_restrict_choices_to or c.value in form_restrict_choices_to -%}
    {%- do options.append({
      'value': c.value|string,
      'text': h.scheming_language_text(c.label) }) -%}
  {%- endif -%}
{%- endfor -%}

{% call form.select(
    field.field_name,
    id='field-' + field.field_name,
    label=h.scheming_language_text(field.label),
    options=options,
    selected=data.get(field.field_name, field.get('default', 'notspecified')),
    error=errors[field.field_name],
    classes=['control-medium'],
    is_required=h.scheming_field_required(field),
    attrs = {'class' : "form-control"}
    )
%}
{% endcall %}
```
### 1.4 แก้ไข ฟิลด์ process ให้ทำงานแบบสัมพันธ์กับ field อื่น
แก้ไขไฟล์ /usr/lib/ckan/default/src/ckanext-thai-gdc/ckanext/thai_gdc/templates/scheming/form_snippets/process.html เพิ่มส่วน javascript และการเรียกใช้
```sh
{% import 'macros/form.html' as form %}
<script type="text/javascript">
	function sSelect_process(){
    index = document.getElementById('field-process').value.split(':')[0];
    var sSub_process = document.getElementById("field-subprocess");
		for (var i = 1; i < sSub_process.length; i++) {
      var txt = sSub_process.options[i].text;
      if(txt.startsWith(index)) {
        $(sSub_process.options[i]).removeAttr('disabled').show();
      } else {
        $(sSub_process.options[i]).attr('disabled', 'disabled').hide();
      }
    }
	}
</script>

{%- set options=[] -%}
{%- set form_restrict_choices_to=field.get('form_restrict_choices_to') -%}
{%- if not h.scheming_field_required(field) or
  field.get('form_include_blank_choice', false) -%}
{%- do options.append({'value': '', 'text': ''}) -%}
{%- endif -%}
{%- for c in h.scheming_field_choices(field) -%}
  {%- if not form_restrict_choices_to or c.value in form_restrict_choices_to -%}
    {%- do options.append({
      'value': c.value|string,
      'text': h.scheming_language_text(c.label) }) -%}
  {%- endif -%}
{%- endfor -%}

{% call form.select(
    field.field_name,
    id='field-' + field.field_name,
    label=h.scheming_language_text(field.label),
    options=options,
    selected=data.get(field.field_name, field.get('default', 'notspecified')),
    error=errors[field.field_name],
    classes=['control-medium'],
    is_required=h.scheming_field_required(field),
    attrs = {'class' : "form-control", 'onchange' : "sSelect_process();", 'onclick' : "sSelect_process();"}
    )
%}
{% endcall %}
```
## 2. การปรับแต่ง Metadata ของ ชุดข้อมูล (2)
### 2.1 สร้างชุดข้อมูลและจัดเตรียม lookup
สร้างไฟล์ /home/osboxes/lookup-process.csv
```sh
process
กระบวนการที่ 1 ยื่นคำขอ
กระบวนการที่ 2 ตรวจประเมินสถานที่ผลิตเครื่องมืออุปกรณ์
กระบวนการที่ 3 ตรวจประเมินคุณภาพสินค้า
กระบวนการที่ 4 บันทึกและส่งผลการตรวจประเมิน
กระบวนการที่ 5 พิจารณาและตัดสินให้การรับรอง
กระบวนการที่ 6 ออกใบรับรอง
```
สร้างไฟล์ /home/osboxes/lookup-subprocess.csv
```sh
process,subprocess
กระบวนการที่ 1 ยื่นคำขอ,กระบวนการย่อยที่ 11 ผู้ผลิต/ผู้รับมอบอำนาจ ยื่นคำขอการรับรอง
กระบวนการที่ 2 ตรวจประเมินสถานที่ผลิตเครื่องมืออุปกรณ์,กระบวนการย่อยที่ 21 ตรวจประเมินสถานที่ผลิต
กระบวนการที่ 2 ตรวจประเมินสถานที่ผลิตเครื่องมืออุปกรณ์,กระบวนการย่อยที่ 22 ตรวจเครื่องมืออุปกรณ์ฯ
กระบวนการที่ 2 ตรวจประเมินสถานที่ผลิตเครื่องมืออุปกรณ์,กระบวนการย่อยที่ 23 ตรวจวัตถุดิบ
กระบวนการที่ 3 ตรวจประเมินคุณภาพสินค้า,กระบวนการย่อยที่ 31 ตรวจคุณภาพทั่วไป
กระบวนการที่ 3 ตรวจประเมินคุณภาพสินค้า,กระบวนการย่อยที่ 32 ทดสอบทางห้องปฏิบัติการ
กระบวนการที่ 4 บันทึกและส่งผลการตรวจประเมิน,กระบวนการย่อยที่ 41 บันทึกผลการตรวจประเมิน
กระบวนการที่ 5 พิจารณาและตัดสินให้การรับรอง,กระบวนการย่อยที่ 51 ผู้ทบทวนฯ พิจารณาและตัดสินให้การรับรอง
กระบวนการที่ 5 พิจารณาและตัดสินให้การรับรอง,กระบวนการย่อยที่ 52 พิจารณาจากผู้ทรงคุณวุฒิ
กระบวนการที่ 6 ออกใบรับรอง,กระบวนการย่อยที่ 61 ออกใบรับรอง
```
สร้างชุดข้อมูล upload ไฟล์ข้อมูลทั้ง 2 ไฟล์ และทดสอบการเรียกใช้ API
```sh
http://{ip}/api/3/action/datastore_search_sql?sql=SELECT process from "{resource_id1}" order by process

http://{ip}/api/3/action/datastore_search_sql?sql=SELECT subprocess from "{resource_id2}" where process = 'กระบวนการที่ 2 ตรวจประเมินสถานที่ผลิตเครื่องมืออุปกรณ์' order by process, subprocess
```
### 2.2 เรียกใช้ข้อมูลจาก DataStore API
เปิดไฟล์ /usr/lib/ckan/default/src/ckanext-thai-gdc/ckanext/thai_gdc/ckan_dataset.json แก้ไขส่วนฟิลด์ process และ subprocess ให้เป็นตามนี้
```sh
{
        "field_name": "process",
        "label": {
          "en": "Process",
          "th": "กระบวนการทำงานหลัก"
        },
        "preset": "select",
        "choices_helper": "scheming_datastore_choices",
        "datastore_choices_resource": "{resource_id1}",
        "datastore_choices_columns": {
          "value": "process",
	        "label": "process"
        },
        "required": true
      },
      {
        "field_name": "subprocess",
        "label": {
          "en": "Sub-Process",
          "th": "กระบวนการทำงานย่อย"
        },
        "preset": "select",
        "choices_helper": "scheming_datastore_choices",
        "datastore_choices_resource": "{resource_id2}",
        "datastore_choices_columns": {
          "value": "subprocess",
          "label": "subprocess"
        }
      }
```
### 2.3 แก้ไข ฟิลด์ process ให้ทำงานแบบสัมพันธ์กับ field อื่น
เปิดไฟล์ /usr/lib/ckan/default/src/ckanext-thai-gdc/ckanext/thai_gdc/ckan_dataset.json แก้ไขฟิลด์ process ให้เป็นตามนี้
```sh
{
        "field_name": "process",
        "label": {
          "en": "Process",
          "th": "กระบวนการทำงานหลัก"
        },
        "form_snippet": "process_from_datastore.html",
        "display_snippet": "select.html",
        "validators": "scheming_required",
        "choices_helper": "scheming_datastore_choices",
        "datastore_choices_resource": "78bb6261-884f-48a1-80ce-1bdd5c727e45",
        "datastore_choices_columns": {
          "value": "process",
	        "label": "process"
        },
        "required": true
      }
```
สร้างไฟล์ /usr/lib/ckan/default/src/ckanext-thai-gdc/ckanext/thai_gdc/templates/scheming/form_snippets/process_from_datastore.html
```sh
{% import 'macros/form.html' as form %}
<script type="text/javascript">
end_point = 'http://164.115.20.130/api/3/action/datastore_search_sql';

  async function getapi(url) { 
    const response = await fetch(url);   
    var data = await response.json(); 
    var records = data.result.records;
    return records;   
  }

	function sSelect_process(){
    index = document.getElementById('field-process').value;
    (async () => {
      records = await getapi(end_point+'?sql=SELECT subprocess as value from "0dc2054f-7fd9-4334-9d04-355ac0c170c4" where process = \''+index+'\' order by subprocess')
      var sSub_process = document.getElementById("field-subprocess");
		  for (var i = 1; i < sSub_process.length; i++) {
        var txt = sSub_process.options[i].text;
        if(records.some(e => e.value == txt)){
          $(sSub_process.options[i]).removeAttr('disabled').show();
        } else {
          $(sSub_process.options[i]).attr('disabled', 'disabled').hide();
      }
    }
    })()
  }
</script>

{%- set options=[] -%}
{%- set form_restrict_choices_to=field.get('form_restrict_choices_to') -%}
{%- if not h.scheming_field_required(field) or
  field.get('form_include_blank_choice', false) -%}
{%- do options.append({'value': '', 'text': ''}) -%}
{%- endif -%}
{%- for c in h.scheming_field_choices(field) -%}
  {%- if not form_restrict_choices_to or c.value in form_restrict_choices_to -%}
    {%- do options.append({
      'value': c.value|string,
      'text': h.scheming_language_text(c.label) }) -%}
  {%- endif -%}
{%- endfor -%}

{% call form.select(
    field.field_name,
    id='field-' + field.field_name,
    label=h.scheming_language_text(field.label),
    options=options,
    selected=data.get(field.field_name, field.get('default', 'notspecified')),
    error=errors[field.field_name],
    classes=['control-medium'],
    is_required=h.scheming_field_required(field),
    attrs = {'class' : "form-control", 'onchange' : "sSelect_process();", 'onclick' : "sSelect_process();"}
    )
%}
{% endcall %}
```