# Config Network on Ubuntu Server

### NetPlan Files

Network interfaces in Ubuntu 20.04 are configured in NetPlan YAML files, which are stored under `/etc/netplan`. The default file networking interfaces for a new Ubuntu 20.04 install is `/etc/netplan/00-installer-config.yaml`.

To edit the default netplan file, use the following command.

```txt
    sudo vi /etc/netplan/00-installer-config.yaml
```

Alternatively, a simpler text editor by the name of nano can be used instead of vim.

```txt
    sudo nano /etc/netplan/00-install-config.yaml
```

### How to Set Static IP Address

The following is an example Netplan file with a network interface that has a static IP address. The interface's name is en01 and it has been assigned static IP addresses `192.168.1.25/24` for IPv4, and `2001:1::1/64` for IPv6.

As both IPv4 and IPv6 have been assigned static IP addresses, each has a gateway set too.

DNS Name servers are also defined in this file. We'll cover DNS a little further down in this tutorial.

```txt
network:
  version: 2
  renderer: networkd
  ethernets:
    en01:
      addresses:
      - 192.168.1.25/24
      - "2001:1::1/64"
      gateway4: 192.168.1.1
      gateway6: "2001:1::2"
      nameservers:
        addresses:
        - 8.8.8.8
        - 8.8.4.4
```

To apply changes to netplan you will need to reload your Netplan network configurations.

```txt
sudo netplan apply
```

**The following example shows how to enable DHCP for both IPv4 and IPv6. To enabled just one, you would remove the network IP version not needed.**

```txt
network:
  version: 2
  renderer: networkd
  ethernets:
    en01:
      dhcp4: true
      dhcp6: true
```

```txt
sudo netplan apply
```

**How to Set DNS**

The following is an example of a network interface id0 with nameservers configured.

```txt
ethernets:
  en01:
    [...]
    nameservers:
      search: [lab, home]
      addresses: [8.8.8.8, "FEDC::1"]
```

* **search** is a list of search domains, which are used when a non-fully qualified hostname is given. For example, if you were to ping server1 rather than server1.lab.
* **addresses** is a list of IPv4 or IPv6 ip addresses for the DNS name servers. IPv6 must be quoted.

**How to set WiFi Authentication**

```txt
ethernets:
  id0:
    [...]
    access-points:
      mode: infrastructure
      bssid: mywifi
      band: 5GHz
      channel: 5
    auth:
      key-management: none | psk | eap 
      password: my-password-string
```

* **mode** set the mode type for your wifi network interface. For connecting to access points the value should be set to infrastructure, which is the default.
* **bassid** is the name of your wifi connection, as configured on your access point.
* **band** is used to set the wireless band. It accepts two values: 5GHz and 2.4GHz. If left unset, the wifi endpoint and your network device will automatically establish the best band. By setting this value you will \* **force** the connection to use a specific band.
* **channel** is used to set your wifi channel, and only takes affect if the band property is set.
* **WPA** and EAP connection modes accept the following configurations.
* **key-management** sets how the supported key management mode.
* **none** to disable key management
* **psk** for WPA with pre-shared key, common for home wifi.
* **eap** for WPA with EAP, which is common for enterprise wifi networks.
* **password** sets the pre-shared key or password for your wifi network, when the mode is set to either psk or eap.
