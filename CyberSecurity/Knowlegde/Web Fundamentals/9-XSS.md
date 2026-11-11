### XSS ชื่อเต็ม Cross site script 
- XSS คือ การโจทมตีที่เก่าแก่อย่างหนึ่งใน web exploit
- แต่ปัจจุบันก็ยังพบช่องโหว่ XSS เยอะเป็นอันดับต้นๆ
- XSS คือ การโจมตี โดยนำ HTML หรือ Java Script  เข้าไปใน client side 
### การทำงานของ XSS
- คือ การนำ script ของ Hacker ไปทำงานบน website ที่มีช่องโหว่
- Hacker จะทำการขโมย cookies หรือ ทำให้เกิดการ redirect user ไปยัง website นอก ที่อาจจะมีการฝั่ง exploit ไว้ เพื่อทำให้เกิด CSRF
- Hacker สามารถทำ XSS ได้ 2 ทาง คือ 1.ฝังผ่าน URL 2.ฝังผ่าน Web board
### ประเภทของ XSS
1. Reflected XSS (Non-persistent XSS)  : script ที่เป็นอันตรายมาจากคำขอ HTTP ปัจจุบัน
2. Stored XSS (Persistent XSS) : script ที่เป็นอันตรายมาจากฐานข้อมูล
3. DOM-based XSS : เป็นช่องโหว่ที่เกิดขึ้นฝั่ง client-side ไม่ใช่ฝั่ง server side
### 1.Reflected XSS

### 2.Stored XSS (Persistent XSS)
- Stored XSS คือ การฝั่ง Script เข้าไปใน website ทั่วไป
- Stored XSS คือ อันตรายที่สุด เพราะ เป็นการโจมตี User ทุกคนที่เข้ามาใช้หน้าที่มีการทำ Stored XSS
- เป้าหมายในการโจมตีแบบ Stored XSS มักจะเป็นการเปลี่ยนหน้าเว็บ
- แตกต่างจาก Reflected XSS ที่มักใช้ในการขโมย Session ID
- Case Study Store XSS
### 3.DOM-Based XSS


### (เพิ่มเติม)Server Side XSS
Server Side XSS เกิดจากการรับ input จาก user เข้าไปสร้าง PDF ซึ่งถ้าเราลอง inject Js code / inject HTML เข้าไป จะทำให้ bot หรือ lib ที่ใช้สร้าง PDF นั้นทำการ execute JS /HTML ที่เรา input เข้าไป

**Payload Server Side XSS**
```

```


### ความรู้เพิ่มเติม 
- Cookies ไม่ใช่ Session ID
- แต่สองอย่างนี้มีความสอดคล้องกัน คือ มันใช้ในการจำลองสถานะในการใช้งานเว็บไซต์
- โดย Cookies คือ ที่เก็บข้อมูลขนาดเล็ก ในฝั่งผู้ใช้ ใน browser ของเรา
	- โดย  Cookies จะอยู่เฉพาะในเครื่องเราเท่านั้น
- Session ID ใช้เก็บ

