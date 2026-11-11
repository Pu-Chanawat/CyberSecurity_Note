### <span style="color:rgb(255, 162, 31)">Linux Path</span>
/ : root directory 
/bin : ใช้เก็บพวก command ทั่วไปที่เอาไว้ใช้ (ห้ามแยก mount ออกจาก root)
/boot : รวม startup ของ linux ไว้
/dev : 
/etc : เก็บ config(การกำหนดค่า)ต่างไว้
/home : home directoy ของ uesr
/lib : เก็บ libary ของ kernel(แกนกลางของระบบปฏิบัติ) และ command ไว้ (ห้ามแยก mount ออกจาก root)
/media : 
/misc :  ส่วนที่เป็น mount point ของพวก local directory ด้วยcommand mount ต่างๆ
/mnt :  
/net : ส่วนที่เป็น mount point ของ network directories ด้วยcommand mount ต่างๆ
/opt :
/proc :
/root : home directoy ของ root
/sbin :  เก็บ system administrator command (ห้ามmount แยกจาก root)
/selinux : เก็บ config ต่างๆของ Security Enchanced Linux
/smb :
/srv :
/tftpboot : path ที่ net server จะมาใช้งาน
/tmp :
/usr :
/var : เก็บพวก log ,mail , printing spool
 
### <span style="color:rgb(255, 162, 31)">ssh</span>
Linux
```
ssh <USER>@<IP>
```
Window
ติดตั้ง PuTTY https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html


### <span style="color:rgb(255, 162, 31)">Network Command</span>
นี้คือ  command ที่เกี่ยวกับ network 
1. <span style="color:rgb(255, 192, 0)">ip</span> : ใช้จัดการ Network
```
ip a                    # แสดง IP ทั้งหมด (เหมือน ifconfig)  
ip link set eth0 up     # เปิด Interface eth0  
ip route show           # แส<span style="color:rgb(255, 192, 0)">ดง Routing Table (เหมือน route -n)  
ip neigh                # แสดง ARP Table  
ip -s link             # แสดงสถิติเค</span>รือข่าย  
```
2. <span style="color:rgb(255, 192, 0)"><span style="color:rgb(255, 192, 0)"> ifconfig</span></span> : ใช้ตรวจสอบและกำหนดค่า **IP Address, MAC Address, Network Interface** (เช่น eth0, wlan0) 
```
ifconfig                  # แสดง Interface ทั้งหมด  
ifconfig eth0             # แสดงเฉพาะ eth0  
ifconfig eth0 192.168.1.100 netmask 255.255.255.0  # ตั้งค่า IP  
ifconfig eth0 up          # เปิดการทำงานของ eth0  
ifconfig eth0 down        # ปิดการทำงานของ eth0  
```
3. <span style="color:rgb(255, 192, 0)">route</span> : ใช้จัดการเส้นทาง network ภายในเครื่อง
```
route -n           # แสดง routing table แบบตัวเลข (ไม่ resolve hostname)
route add default gw 192.168.1.1 eth0  # เพิ่ม default gateway
route del -net 10.0.0.0 netmask 255.0.0.0  # ลบ route
```
4. <span style="color:rgb(255, 192, 0)"> ping</span> : ส่ง ICMP ECHO_REQUEST ไปยังปลายทาง ใช้เพื่อตรวจสอบว่าเครื่องปลายทางนั้นยังเปิดอยู่หรือไม่
```
ping [IP หรือ Domain]  

Example
ping google.com      # ทดสอบเชื่อมต่อ Google
ping 8.8.8.8        # ทดสอบเชื่อมต่อ DNS ของ Google

ping -c 10 -i 0.2 google.com  # ส่ง 10 Packet ทุก 0.2 วินาที
ping -s 1500 8.8.8.8          # ส่ง Packet ขนาด 1500 ไบต์
ping -q -c 100 google.com     # แสดงผลสรุปหลังส่ง 100 Packet

```
5. <span style="color:rgb(255, 192, 0)"><span style="color:rgb(255, 192, 0)">i</span>perf</span> : วิเคราะห์ network performance ระหว่าง 2 เครื่อง (client กับ sever)
   client 
```
iperf -c [เซิร์ฟเวอร์_IP]  # เชื่อมต่อไปยังเซิร์ฟเวอร์
```
server
```
iperf -s  # เปิดโหมดเซิร์ฟเวอร์ (รอรับการเชื่อมต่อ)
```
ตัวอย่างการใช้จริง 
```
# เครื่อง A (เซิร์ฟเวอร์):
iperf -s

# เครื่อง B (ไคลเอนต์):
iperf -c 192.168.1.100 -t 30 -i 2  # ส่งข้อมูล 30 วินาที, แสดงผลทุก 2 วินาที
```
6. <span style="color:rgb(255, 192, 0)">traceroute</span> : ใช้ตรวจสอบเส้นทางการส่งข้อมูล 
```
traceroute [ปลายทาง]  
```
 ตัวอย่าง
```
traceroute google.com  
traceroute 8.8.8.8  
```
7. <span style="color:rgb(255, 192, 0)">wget</span> : dowload ไฟล์จาก web
 ```
 wget https://example.com/data.txt
```
8. <span style="color:rgb(255, 192, 0)">curl</span> : 
```
curl https://example.com          # ดึง HTML จากเว็บ  
curl -o file.txt https://example.com/data.txt  # ดาวน์โหลดไฟล์  
curl -X POST -d "name=John" https://api.example.com  # ส่ง POST Request  
curl -I https://example.com       # ดู Header เฉพาะ  `
```
9. <span style="color:rgb(255, 192, 0)">netstat</span> : ใช้ตรวจสอบ **การเชื่อมต่อเครือข่าย (network connections)**, **พอร์ตที่เปิด**, **สถิติการใช้งานเครือข่าย** และ **Routing Table** ในระบบ
```
netstat -tulnp    # ดูพอร์ตที่เปิดและโปรแกรมที่ใช้ (TCP/UDP)  
netstat -anp      # แสดงการเชื่อมต่อทั้งหมด (รวม IP และ PID)  
netstat -s        # แสดงสถิติการใช้งานเครือข่าย  
netstat -r        # แสดง Routing Table (เหมือน route -n)  
```

10. <span style="color:rgb(255, 192, 0)">ip + route</span> : 
```
ip route show      # แสดง routing table (แบบใหม่)
ip route add 192.168.1.0/24 via 10.0.0.1  # เพิ่ม route
```

|ความสามารถ|`ifconfig`|`ip` (สมัยใหม่)|
|---|---|---|
|ดู IP|✅|✅ (`ip a`)|
|ตั้งค่า IP|✅|✅ (`ip addr add`)|
|ดู Routing|❌|✅ (`ip route`)|
|ดู ARP Table|❌|✅ (`ip neigh`)|
