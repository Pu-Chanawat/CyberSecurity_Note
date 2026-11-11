#### What is DNS?
DNS คืออะไร ?
	DNS (Domain Name System)
	DNS สมุดโทรศัพท์ของ Internet
คือ ระบบจำชื่อเว็บไซต์ได้ง่ายๆ โดยไม่ต้องจำเป็นตัวเลข IP address ให้ซับซ้อน
- IP Address : เหมือน"เลขที่บ้าน" ซึ่งจะมีชุดตัวเลขเฉพาะ เช่น 104.26.10.299
- Domain Name : เปรียบเสมือน"ชื่อบ้าน" ซึ่งทำให้เราจำง่าย เช่น  Tryhackme.com
หน้าที่ของ DNS ?
คือ เป็นล่าม แปลง Web Name เช่น Tryhackme.com ให้กลายเป็น IP address ของเว็บไซต์ เพื่อให้คอมคุยกันรู้เรื่อง
#### Domain Hierarchy
ลำดับชั้นของ Domain (ดูจากขวาไปซ้าย)
![[Pasted image 20260407005032.png]]
1. TLD (Top-Level Domain)
	เป็นส่วนที่อยู่ขวาสุดของ Domain Name เช่น .com .net .th 
	เช่น .com ใน Tryhackme.com
	ซึ่งแบ่งออกเป็น 2 ส่วน
		- gTLD (Generic) : บอก"ประเภท"ของเว็บไซต์ เช่น
			- .com (Commercial) - เพื่อการค้า
			- .org (Organization) - องค์กรไม่แสวงหากำไร
			- .edu (Education) - การศึกษา
		- ccTLD (County Code) : บอกประเทศที่ตั้ง เช่น
			- .th (Thailand)
			- .jp (๋Japan)
2. SLD (Second-Level Domain)
	คือชื่อเว็บไซต์ เช่น Tryhackme ใน Tryhackme.com
	- ข้อจำกัด : ยาวได้ไม่เกิน 63 ตัวอักษร
	- ตัวอักษรที่ใช้ได้ : a-z,0-9, -(ยัติภังค์)
	- ข้อห้าม : ห้ามใช้ _  (Underscore) และห้ามใช้ - ขึ้นต้นหรือลงท้ายชื่อ
3. Sudomain
	คือส่วนที่อยู่ซ้ายสุด ใช้สำหรับแบ่งส่วนบริการภายในเว็บไซต์
	เช่น admin ใน admin.Tryhackme.com
	- การใช้งาน : สามารถสร้างได้ไม่จำกัดจำนวน และ ซ้อนกันได้หลายชั้น เช่น dev.api.example.com
	- ข้อจำกัด : แต่ละส่วนต้องยาวไม่เกิน  63 ตัวอักษร และความยาวรวมทั้งหมดของโดเมนต้องไม่เกิน 253 ตัวอักษร
#### DNS Record Types
DNS ไม่ได้แค่เก็บชื่อเว็บ แต่มี"บันทึก"หลายประเภทเพื่อบอกว่าโดเมนนี้ใช้ทำอะไรบ้าง

| Recond Type  | หน้าที่หลัก (Function)                           | ตัวอย่าง (Example)                                     |
| ------------ | ------------------------------------------------ | ------------------------------------------------------ |
| A Record     | แปลง Domain Name เป็น IPv4 address               | 104.26.10.229                                          |
| AAAA Record  | แปลง Domain Name เป็น IPv6 address               | 2606:4700:20::681a:be5                                 |
| CNAME Record | ชี้ โดเมนหนึ่ง ไปยังอีก โดเมนหนึ่ง               | store.thme.com -> shops.shopify.com                    |
| MX Record    | ระบุเครื่อง Mail Server ที่รับผิดอีเมลของ Domain | alt1.aspmx.l.google.com (มี Priority บอกลำดับก่อนหลัง) |
| TXT REcord   | เก็บข้อมูลในรูปแบบ Text                          | ใช้ยืนยันเจ้าของโดเมน หรือ ตั้งค่าความปลอดภัยอีเมล     |
อธิบายเพิ่มเติม
1. MX (Mail Exchange)
   หัวใจของระบบนี้คือ Priority Flag เพื่อถ้าหากเกิน server down  ให้ส่ง Backup Server ตัวไหนต่อ
2. TXT (Text Record)
   เกี่ยวกับ Security
	- Verify Ownership : ยืนยันตัวตนว่าเป็นเจ้าของ Domain จริงๆ ในบริการเช่น Google Workspace , Azure
	- Email Security : ใช้ตั้งค่า SPF , DKIM , DMARC เพื่อป้องกันคนอื่นปลอมแปลงอีเมล (Email Spoofing) ส่งในนาม Domain ของเรา
 สรุปสั้นๆ
 - CNAME = ชื่อเล่นของชื่ออื่น
 - MX = ส่งจดหมาย (Email)
 - TXT = ข้อความยืนยัน/ความปลอดภัย
#### DNS Request

เส้นทางการทำงานของ DNS Request ?
1. การตรวจสอบ Local Cache (ที่เครื่องเราเอง)
   ก่อนจะออกไปถามคนอื่น คอมจะเช็ค Local Cache ในเครื่องตัวเองก่อนว่าเคยจด IP นี้ไว้ไหม ถ้ามีก็จบ ถ้าไม่ก็ไปขั้นตอนต่อไป
2. สอบถาม Recursive DNS Server
   เครื่องเราจะส่งคำขอไปที่ Recursive DNS Server (ส่วนใหญ่จะเป็น ISP คือผู้ให้บริการเน็ตเรา )
	- หาก Server นี้มีข้อมูลใน Cache มันจะส่งคำตอบกลับมาทันที
	- ถ้าไม่มีก็ไปหาต่อในขั้นตอนต่อไป
3. สอบถาม Root DNS Server (กระดูกสั้นหลังของ Internet)
   Recursive Server จะไปถาม  Root Server เป็นที่แรก 
   Root Server ไม่รู้ IP เว็บ แต่รู้ว่าใครดูแล TLD นั้น
	- ตัวอย่าง : ถ้าขอ .com 
	  Root Server จะส่งเราไปหา TLD Server ของ .com
4. สอบถาม TLD Server (ผู้ดูแลนามสกุล)
   เราจะไปถาม TLD Server ต่อว่าใครเป็นคนดูแล Domain นี้ (Ex. Tryhackme.com)
5. สอบถาม Authoritative DNS Server (เจ้าของข้อมูลตัวจริง)
	   - TLD Server จะตอบกลับมาเป็นชื่อของ Autheritative DNS Server (หรือ Nameserver) ของ Domain นั้น
6. การทำ Cac้นhig และ ส่งคำตอบกลับ
	   มันจะส่ง IP Address (Ex. A Record) กลับไปยัง Recursive Server

กลไกลการจำข้อมูล TTL (Time To Live) ?
คือ ทุกครั้งที่ได้รับคำตอบ DNS จะมีค่า  TTL แนบมาด้วยเสมอ (เป็นหน่วยวินาที) ซึ่งเป็นตัวบอกว่า"ให้จำข้อมูลนี้ไว้นานแค่ไหน"ก่อนที่จะต้องถามใหม่ เพื่อให้มั่นใจว่าข้อมูลจะไม่อัปเดตช้าจนเกินไป

สรุป ?
1. เครื่องเรา (มีไหม ?)
2. ISP (มีไหม ?)
3. Root (ไปหา .com นู่น)
4. TLD (ไปหา Nameserver ของเจ้านี้ดูสิ)
5. Authoritative (เจอ IP แล้ว)
6. Caching และ ตอบกลับ