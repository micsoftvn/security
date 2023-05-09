# Kiểm thử mạng

#### Check và kiểm tra mạng, kiểm tra phản hồi các máy chủ

Ở khu vực này chúng ta sẽ kiểm tra và phản hồi của các máy chủ bằng cách sử dụng giao thức ICMP cơ bản

#### ICMP

```
ping -c 1 192.168.1.3    # 1 echo request to a host
fping -g 192.168.1.0/24  # Send echo requests to ranges
nmap -PEPM -sP -n 192.168.1.0/24 #Send echo, timestamp requests and subnet mask requests
```

#### Khám phá các cổng khác ( TCP port )

Có nhiều cách để kiểm tra port mở trên máy chủ, tuy nhiên để kiểm tra trạng thái các port máy chủ nhanh ta dùng (massscan ) hoặc Nmap

```
masscan -p20,21-23,25,53,80,110,111,135,139,143,443,445,993,995,1723,3306,3389,5900,8080 192.168.1.0/24
```

Sử dụng masscan , kiểm tra 20 port thông dụng

#### UDP Port

Một vài phương thức kiểm tra port UDP

```
nmap -sU -sV --version-intensity 0 -F -n 192.168.1.0/24
```

\-sV sẽ thực hiện kiểm tra nmap từng gói dịch vụ UDP đã biết

"--version-intensity 0" sẽ khiến nmap chỉ kiểm tra khả năng xảy ra cao nhất

Hoặc kiểm tra UDP với netcat

```
nc -vz -u 8.8.8.8 53
# Connection to 8.8.8.8 53 port [udp/domain] succeeded!
```

#### Kiểm tra SCTP Port

```
nmap -T4 -sY -n --open -Pn <IP/range>
```

### Kiểm tra các máy chủ trong hệ thống

Trường hợp tốt khi bạn đã nằm trong vùng mạng để check và kiểm tra các máy chủ khác trong vùng.

#### Passive

```
netdiscover -p
p0f -i eth0 -p -o /tmp/p0f.log
# Bettercap
net.recon on/off #Read local ARP cache periodically
net.show
set net.show.meta true #more info
```

#### Active

Ở đầy thường là ccs kỹ thuật kiểm tra khi ở ngoài vùng mạng

```
#ARP discovery
nmap -sn <Network> #ARP Requests (Discover IPs)
netdiscover -r <Network> #ARP requests (Discover IPs)

#NBT discovery
nbtscan -r 192.168.0.1/24 #Search in Domain

# Bettercap
net.probe on/off #Discover hosts on current subnet by probing with ARP, mDNS, NBNS, UPNP, and/or WSD
set net.probe.mdns true/false #Enable mDNS discovery probes (default=true)
set net.probe.nbns true/false #Enable NetBIOS name service discovery probes (default=true)
set net.probe.upnp true/false #Enable UPNP discovery probes (default=true)
set net.probe.wsd true/false #Enable WSD discovery probes (default=true)
set net.probe.throttle 10 #10ms between probes sent (default=10)

#IPv6
alive6 <IFACE> # Send a pingv6 to multicast.
```

#### Active ICMP

Sử dụng nmap với cờ -PEPM kiểm tra trạng thái máy chủ với ICMP toàn dải

```
nmap -PEMP -sP -vvv -n 192.168.1.0/24
```

#### Wake on Lan

Thường sử gửi các gói tin tới cạc mạng (ethernet 0x0842 ) hoặc gói tin UDP tới port 9

<pre><code><strong>#!/bin/bash
</strong><strong>MAC=11:22:33:44:55:66
</strong>Broadcast=255.255.255.255
PortNumber=4000
echo -e $(echo $(printf 'f%.0s' {1..12}; printf "$(echo $MAC | sed 's/://g')%.0s" {1..16}) | sed -e 's/../\\x&#x26;/g') | nc -w1 -u -b $Broadcast $PortNumber
</code></pre>

#### TCP

* **Open** port: _SYN --> SYN/ACK --> RST_
* **Closed** port: _SYN --> RST/ACK_
* **Filtered** port: _SYN --> \[NO RESPONSE]_
* **Filtered** port: _SYN --> ICMP message_

```bash
# Nmap fast scan for the most 1000tcp ports used
nmap -sV -sC -O -T4 -n -Pn -oA fastscan <IP> 
# Nmap fast scan for all the ports
nmap -sV -sC -O -T4 -n -Pn -p- -oA fullfastscan <IP> 
# Nmap fast scan for all the ports slower to avoid failures due to -T4
nmap -sV -sC -O -p- -n -Pn -oA fullscan <IP>

#Bettercap Scan
syn.scan 192.168.1.0/24 1 10000 #Ports 1-10000
```

#### UDP

Với UDP có 2 cách để kiểm tra:

* Gửi gói tin UDP và check phản hồi từ ICMP
* Gửi một định dạng UDP ( formatted datagrams ) đợi phản hồi từ dịch vụ như ( DNS, DHCP, TFTP )

Sử dụng Nmap kiểm tra port với cờ "sV" kiểm tra cả UDP và TCP

```
# Nmap check 100 port udp phổ biến
nmap -sU -sV --version-intensity 0 -n -F -T4 <IP>
# Nmap check 100 port UDP phổ biến và chạy script
nmap -sU -sV -sC -n -F -T4 <IP> 
# Check kiểm tra nhanh 1000 port UDP
nmap -sU -sV --version-intensity 0 -n -T4 <IP>
# Kiểm tra tất cả các port UDP
```

REF: [https://github.com/micsoftvn/udp-proto-scanner](https://github.com/micsoftvn/udp-proto-scanner)

#### SCTP

**Stream Control Transmission Protocol (SCTP) là một giao thức truyền tải nhiều luồng dữ liệu cùng một lúc giữa hai thiết bị đầu cuối đã thiết lập kết nối trong mạng**. Chúng ta cũng có thể gọi SCTP là “TCP thế hệ mới” (next generation TCP) hay TCPng.

```
# Nmap Scan nhanh
nmap -T4 -sY -n -oA SCTFastScan <IP>
# Nmap scan tất cả các port SCTP
nmap -T4 -p- -sY -sV -sC -F -n -oA SCTAllScan <IP>
```

#### Tìm kiếm và khám phá địa chị IP nội bộ

Các bộ định tuyến, tường lửa và thiết bị mạng được định cấu hình sai đôi khi phản hồi các đầu dò mạng bằng địa chỉ nguồn không công khai. Bạn có thể sử dụng tcpdump được sử dụng để xác định các gói nhận được từ các địa chỉ riêng tư trong quá trình thử nghiệm. Trong trường hợp này, giao diện eth2 trong Kali Linux có thể định địa chỉ từ Internet công cộng (Nếu bạn đứng sau NAT của Tường lửa thì loại gói này có thể sẽ bị lọc).

```
tcpdump –nt -i eth2 src net 10 or 172.16/12 or 192.168/16
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth2, link-type EN10MB (Ethernet), capture size 65535 bytes
IP 10.10.0.1 > 185.22.224.18: ICMP echo reply, id 25804, seq 1582, length 64
IP 10.10.0.2 > 185.22.224.18: ICMP echo reply, id 25804, seq 1586, length 64
```

#### Sniffing

Sniffing bạn có thể tìm hiểu chi tiết về dải IP, kích thước mạng con, địa chỉ MAC và tên máy chủ bằng cách xem xét các gói và khung đã chụp. Nếu mạng bị định cấu hình sai hoặc cấu trúc chuyển đổi bị căng thẳng, kẻ tấn công có thể lấy được tài liệu nhạy cảm thông qua việc đánh hơi mạng thụ động. Nếu mạng Ethernet chuyển mạch được cấu hình đúng cách, bạn sẽ chỉ thấy các khung và nội dung quảng bá dành cho địa chỉ MAC của mình.

#### TCPDump

```
sudo tcpdump -i <INTERFACE> udp port 53 #Listen to DNS request to discover what is searching the host
tcpdump -i <IFACE> icmp #Listen to icmp packets
sudo bash -c "sudo nohup tcpdump -i eth0 -G 300 -w \"/tmp/dump-%m-%d-%H-%M-%S-%s.pcap\" -W 50 'tcp and (port 80 or port 443)' &"
```

Hoặc bạn có thể bắt gói tin phiên đăng nhập SSH với Wireshark

```
ssh user@<TARGET IP> tcpdump -i ens160 -U -s0 -w - | sudo wireshark -k -i -
ssh <USERNAME>@<TARGET IP> tcpdump -i <INTERFACE> -U -s0 -w - 'port not 22' | sudo wireshark -k -i - # Exclude SSH traffic
```

#### Bettercap

```
net.sniff on
net.sniff stats
set net.sniff.output sniffed.pcap #Write captured packets to file
set net.sniff.local  #If true it will consider packets from/to this computer, otherwise it will skip them (default=false)
set net.sniff.filter #BPF filter for the sniffer (default=not arp)
set net.sniff.regexp #If set only packets matching this regex will be considered
```

#### Wireshark

:D tất nhiên là bạn có thể bắt gói tin với công cụ Wireshark rồi

#### Bắt gói tin với thông tin đăng nhập

Sử dụng tools [https://github.com/micsoftvn/PCredz](https://github.com/micsoftvn/PCredz) để lọc dữ liệu bao gồm thông tin đăng nhặp từ Pcap

#### LAN attacks

#### ARP spoofing

**ARP spoofing có thể cho phép kẻ tấn công chặn các khung dữ liệu trên mạng, sửa đổi lưu lượng,** hoặc **dừng tất cả lưu lượng.** Thông thường cuộc tấn công này được sử dụng như là một sự mở đầu cho các cuộc tấn công khác, chẳng hạn như tấn công từ chối dịch vụ

#### Bettercap

```
arp.spoof on
set arp.spoof.targets <IP> #Specific targets to ARP spoof (default=<entire subnet>)
set arp.spoof.whitelist #Specific targets to skip while spoofing
set arp.spoof.fullduplex true #If true, both the targets and the gateway will be attacked, otherwise only the target (default=false)
set arp.spoof.internal true #If true, local connections among computers of the network will be spoofed, otherwise only connections going to and coming from the Internet (default=false)
```

