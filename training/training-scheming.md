## การปรับแต่ง Metadata ของ ชุดข้อมูล
### 1. เพิ่มฟิลด์แบบ dropdown ของ process
เปิดไฟล์ /usr/lib/ckan/default/src/ckanext-thai-gdc/ckanext/thai_gdc/ckan_dataset.json แทรกโค้ดนี้
```sh
,
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
        "preset": "select"
      }
```
### 2. แก้ไข ฟิลด์ process ให้ทำงานตาม template html ที่กำหนด
เปิดไฟล์ /usr/lib/ckan/default/src/ckanext-thai-gdc/ckanext/thai_gdc/ckan_dataset.json แก้ไขส่วนฟิลด์ process ให้เป็นตามนี้
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