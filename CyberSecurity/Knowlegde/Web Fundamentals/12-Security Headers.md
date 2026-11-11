### X-Frame-Options
X-Frame-Options เป็น HTTP Header ที่ใช้ป้องกัน Clickjacking โดยควบคุมว่าเว็บไซต์ของเราสามารถถูกฝังใน `<iframe>` ได้หรือไม่ และจากแหล่งใดบ้าง

**ค่าที่รองรับมีเพียง 3 แบบ:**
1. deny – ไม่อนุญาตให้แสดงหน้าเว็บใน iframe เลย ไม่ว่ามาจากไหน
	`X-Frame_options : deny`
2. sameorigin – อนุญาตให้แสดงหน้าเว็บใน iframe เฉพาะเมื่อมาจาก domain เดียวกัน
    `X-Frame_options : sameorigin
3. allow-from `<uri>` – อนุญาตให้ iframe จาก URI ที่ระบุเท่านั้น (เบราว์เซอร์บางตัวอาจไม่รองรับ
    `X-Frame_options : allow-from https://www.example.com/

---
**ตรวจ IP หลัง Proxy ด้วย X-Forwarded-For**
- โดยปกติ IP ผู้ใช้ใน PHP จะดูจาก `$_SERVER['HTTP_CLIENT_IP']`
- ถ้ามี Proxy/Load Balancer → จะมี header ชื่อ **X-Forwarded-For (XFF)** ที่เก็บ IP จริงของผู้ใช้
- นอกจาก XFF ยังมี header อื่น ๆ ที่ใช้ระบุ IP ได้ เช่น
    - `X-Originating-IP`
    - `X-Remote-IP`
    - `X-Remote-Addr`
👉 เวลาเช็ก IP ควรตรวจทั้ง `HTTP_CLIENT_IP`, `HTTP_X_FORWARDED_FOR` และ header อื่น ๆ ประกอบกันเพื่อหาต้นทางจริงของผู้ใช้.

จากภาพเราสามารถใช้<mark style="background: #FFB86CA6;"> X-Forwarded-For เพื่อ ปลอบ IP ได้</mark>
![[Pasted image 20250821112640.png]]