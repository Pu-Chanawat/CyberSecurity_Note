## **Challenge Info**
- Name: Flag Command
- Category: Web
- Medium
## **Description**
Try to see if you can login as admin!

ข้อนี้ดูจากทำอธิบายโจทย์ คือมันต้องการให้เราทำอะไรสักอย่างเกี่ยวกับหน้า login ซึ่งคาดการว่าเป็นการเข้าหน้า admin ให้ได้

## Reconnaissance
ค้นหาข้อมูลพบ
http://fickle-tempest.picoctf.net:57122/login.html เป็นหน้า login น่าสนใจ
กับ
![[Pasted image 20260624003900.png|229]]

## Analysis
จากการพบหน้า login มันมี 1 ช่องโหว่ที่ผมนึกออกคือ SQL injection Login Bypass ก็เลยลอง ได้ผลลัพท์ ผ่านได้คำตอบในข้อของ Irish-Name-Repo 1-2 แต่ 3 พบว่ามันขึ้นข้อความดังด้านล่าง

==**admin'--**==
Login failed.

==admin'OR'1'='1==
**Warning**: SQLite3::query(): Unable to prepare statement: 1, ==near "BE"==: syntax error in **/var/www/html/login.php** on line **20**  
  
**Fatal error**: Uncaught Error: Call to a member function fetchArray() on boolean in /var/www/html/login.php:21 Stack trace: #0 {main} thrown in **/var/www/html/login.php** on line **21**

จาก error พบว่า เราให้ OR แต่มันกลับขึ้น BE ซึ่งว่ามันมีการเลื่อนตัวอักษร มีการ encrypted
เลยทำการ
## Exploitation
![[Pasted image 20260624004543.png|281]]
ROT 13 จาก admin'OR'1'='1 เป็น nqzva'BE'1'='1
แล้วลองอีกครั้งก็ได้ Flag ออกมาแล้ว

## Attack Chain