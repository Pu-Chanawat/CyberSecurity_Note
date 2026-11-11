#### Introducing LAN Topologies
LAN คือการส่งข้อมูลอะ ซึ่งมีหลายวิธี หลายอุปกรณ์ที่ใช้

3 รูปแบบ
- Star Topology
  ![[Pasted image 20260325221320.png|307]]
  
- Bus Topology
  ![[Pasted image 20260416060941.png|333]]
  
- Ring Topology
  ![[Pasted image 20260325221519.png|315]]

อุปกรณ์
- Switch
- Router
![[Pasted image 20260325221645.png|369]]


#### subnetting
subnetting คือ ส่วนย่อยของ network
#### ARP
**ARP คืออะไร ?**
ARP (Address resolution Protocol) คือ เทคโนโลยีที่ทำให้อุปกรณ์สามารถระบุตัวตนใน networking ได้
โดย ARP จะทำให้อุปกรณ์สามารถจับคู่  MAC address ของตน กับ IP address ใน network ได้

**ARP ทำงานอย่างไร ?**
เกรนคราวๆ อุปกรณ์ทุกตัวใน network จะสมุดบันทึกของตัวเองเรียกว่า Cache 
ซึ่งในบริบทของ ARP , ARP จะใช้ Cache เพื่อเก็บข้อมูลที่ใช้ระบุตัวตน

เพื่อให้สามารถจับคู่กันได้ ARP จะส่งข้อความไป 2 อย่างคือ
1. ARP request
2. ARP reply
   
โดยจะมีกระบวนการดังนี้
1. อุปกรณ์จะทำการ ARP request ไปแบบ broadcast เพื่อถามอุปกรณ์ทุกตัวว่า ใครเป็นเจ้าของ  IP address นี้ ช่วยบอก MAC adrress ของคุณหน่อย
2. เมื่ออุปกรณ์ที่เป็นเจ้าของ IP address นั้นได้รับข้อความก็จะทำการ ARP reply ส่ง MAC address ของตัวเองตอบกลับไป
![[Pasted image 20260326143843.png|396]]

#### DHCP
DHCP ย่อมาจาก Dynamic host configuration protocol
![[Pasted image 20260327001128.png|437]]
IP address ของอุปกรณ์ ถูกกำหนดได้ 2 วิธีคือ
1.แบบ Manual คือการกำหนดด้วยตัวเอง พิมพ์เอง
2.แบบ DHCP server

ซึ่งแบบ DHCP มีหลักการทำงานดังนี้
1.อุปกรณ์ที่ยังไม่มี IP address (ไม่มีการทำแบบ Manual) เชื่อมเข้า network
2.อุปกรณ์นั้นจะส่งคำร้องขอออกไปใน network เพื่อดูว่ามี DHCP server ตัวไหนอยู่ใน network บ้าง (DHCP Discover) 
3.DHCP server นั้นจะทำการตอบกลับพร้อมเสนอ IP address ให้อุปกรณ์ (DHCP Offer) 
4.อุปกรณ์นั้นจะตอบกลับเพื่อยืนยันว่าต้องการใช้ IP address ที่เสนอมา (DHCP request)
5.DHCP server ตอบกลับรับทราบว่าสิ้นสุดกระบวนการแล้ว และ อุปกรณ์นั้นก็สามารถนำ IP address ไปใช้ได้เลย (DHCP ACK)