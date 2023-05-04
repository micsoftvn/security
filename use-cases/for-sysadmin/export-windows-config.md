# Export Windows Config

```
@echo on
@echo ==============================================
@echo = Report Windows Config....................=
@echo = Please wait a moment.......................=
@echo = Write by Micsoftvn.......................=
@echo ==============================================
@echo off

mkdir C:\Report
Rem ====GET INFO===========

Rem ====GET INFO 2===========
wmic SHARE GET name,AllowMaximum,Description,Name,Path,Status  /format:htable >>"C:\Report\INFO.htm

Rem ====GET INFO 3-4 ===========
wmic UserAccount where (Name="Administrator") get Description,Name,PasswordExpires,Status /format:htable >>"C:\Report\INFO.htm
wmic UserAccount where (Name="Guest") get Description,Name,PasswordExpires,Status /format:htable >>"C:\Report\INFO.htm

Rem ====GET INFO 5=========================
gpresult /H C:\Report\LocalPolicy.html

Rem ====GET INFO 6================
reg query HKLM\SYSTEM\CurrentControlSet\Control\Lsa /v restrictanonymous >>"C:\Report\regedit.html
rem reg query HKLM\SYSTEM\CurrentControlSet\Control\SecurePipeServers\winreg >>"C:\Report\regedit.html
reg query HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters /v DisableIPSourceRouting >>"C:\Report\regedit.html
reg query HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters /v PerformRouterDiscovery >>"C:\Report\regedit.html

Rem ==== GET INFO 7=======================
wmic service where (Name="WerSvc") get caption,name,startmode,state /format:htable >>"C:\Report\INFO.htm

Rem ====GET INFO 8=================================
wmic service where (Name="Browser") get caption,name,startmode,state /format:htable >>"C:\Report\INFO.htm
wmic service where (Name="wuauserv") get caption,name,startmode,state /format:htable >>"C:\Report\INFO.htm
wmic service where (Name="lmhosts") get caption,name,startmode,state /format:htable >>"C:\Report\INFO.htm

Rem ====GET INFO 9=====================
w32tm /query /status >>"C:\Report\regedit.html

Rem ====GET INFO 10================
wmic qfe get Description, HotFixID, InstalledBy, InstalledOn /format:htable >>"C:\Report\INFO.htm
wmic qfe where (HotFixID="KB4056895") get Description, HotFixID, InstalledBy, InstalledOn /format:htable >>"C:\Report\INFO.htm
wmic qfe where (HotFixID="KB4088876") get Description, HotFixID, InstalledBy, InstalledOn /format:htable >>"C:\Report\INFO.htm

Rem ====GET INFO 11========================

wmic service where (Name="masvc") get caption,name,startmode,state /format:htable >>"C:\Report\INFO.htm
wmic service where (Name="HipMgmt") get caption,name,startmode,state /format:htable >>"C:\Report\INFO.htm
wmic service where (Name="McShield") get caption,name,startmode,state /format:htable >>"C:\Report\INFO.htm
wmic service where (Name="enterceptAgent") get caption,name,startmode,state /format:htable >>"C:\Report\INFO.htm
wmic service where (Name="scsrvc") get caption,name,startmode,state /format:htable >>"C:\Report\INFO.htm

Rem ====GET INFO 12===============
wmic service where (Name="arc_Connector") get caption,name,startmode,state /format:htable >>"C:\Report\INFO.htm
wmic service where (Name="arc_windowsunified") get caption,name,startmode,state /format:htable >>"C:\Report\INFO.htm
wmic service where (Name="arc_windowsfg") get caption,name,startmode,state /format:htable >>"C:\Report\INFO.htm
wmic service where (Name="arc_nt_local") get caption,name,startmode,state /format:htable >>"C:\Report\INFO.htm
```
