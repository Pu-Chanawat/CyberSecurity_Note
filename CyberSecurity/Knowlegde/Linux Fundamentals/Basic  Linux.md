**Network**
- ip 
- ifconfig 
- iperf 
- wget 
- curl 
- netstat 
- ss 
- iptables 
**File System**
- ls
- df
- cat
- echo
- mv
- cp
- find
  

`find / -type f -name "*.txt" 2>/dev/null`
/ -> เริ่มหาตั้งแต่ /
-type -f ->เลือกเฉพาะไฟล์
-name "*.txt" -> หาชื่อไฟล์ที่ลงท้ายด้วย .txt
2>/dev/null -> ตัด error เช่น Permission denied ออก