# View Wifi Network Connection

View wifi network connection on Ubuntu

```txt
cd /etc/NetworkManager/system-connections
```

In this folder, you can see any list file with name of wifi network Example read one file

```txt
cat /etc/NetworkManager/system-connections/LinksysSMB24G.nmconnection
```

```txt
[ipv6]
method=auto
[connection]
id=SSID #(e.g.EDUroam)
uuid=9e123fbc-0123-46e3-97b5-f3214e123456 #unique uuid will be created upon creation of this profile
type=802-11-wireless
[802-11-wireless-security]
key-mgmt=wpa-eap
auth-alg=open
[802-11-wireless]
ssid=SSID
mode=infrastructure
mac-address=0A:12:3C:DA:C1:A5
security=802-11-wireless-security
[802-1x]
eap=peap;
identity=studentid123123
phase2-auth=mschapv2
password=mypass123123
[ipv4]
method=auto
```
