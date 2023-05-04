# USB drive with QEMU

**Find the Bus and Device ID:**

```txt
$ lsusb
...
Bus 001 Device 008: ID 0781:5151 SanDisk Corp. Cruzer Micro Flash Drive
...
```

**Boot with QEMU:**

```txt
sudo qemu-system-x86_64 -m 512 -enable-kvm -usb -device usb-host,hostbus=1,hostaddr=8
```

Remember : Correct with hostbus and hostaddr
