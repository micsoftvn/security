# Fail2Ban Cheat Sheet

Note:

* When using Docker you can change the log driver to syslog

Typical file paths:

```
/etc/fail2ban/action.d/
/etc/fail2ban/jail.d/
/etc/fail2ban/filter.d/

```

Get Fail2Ban status and list all jails:

```
fail2ban-client status
```

List all IPs in a specific jail:

```
fail2ban-client status <JAIL-NAME>
```

Unban a specific IP from a jail:

```
fail2ban-client set <JAIL-NAME> unbanip <IP-ADDRESS>
```

Unban a IP from all jails:

```
fail2ban-client unban 49.179.29.27
```

Ban a specific IP in a jail:

```
fail2ban-client set <JAIL-NAME> banip <IP-ADDRESS>
```

Datefilter expression:

```
%%d/%%m/%%Y:%%H:%%M:%%S
```

Test a fail2ban regex when using Docker, Portainer, JSON logs:

```
fail2ban-regex /var/logDocker/de5c71e9daaa/de5c71e9daaa-json.log wordpress-custom.conf
```

Test a fail2ban filter within the container or on same host as fail2ban:

```
fail2ban-regex /var/log/syslog /data/filter.d/bad-bots.conf 
```

Example fail2ban filter test:

```
Running tests
=============

Use   failregex filter file : bad-bots, basedir: /data
Use      datepattern : .*- - %d/%m/%Y:%H:%M:%S : .*- - Day/Month/Year:24hour:Minute:Second
Use         log file : /var/log/syslog
Use         encoding : UTF-8

Results
=======

Failregex: 160 total
|-  #) [# of hits] regular expression
|   1) [160] ^.*F2B\[.*\]\: <HOST> - -.*(GET|POST).*HTTP.*(?:atSpider/1\.0|autoemailspider|China Local Browse 2\.6|ContentSmartz|DataCha0s/2\.0|DBrowse 1\.4b|DBrowse 1\.4d|Demo Bot DOT 16b|Demo Bot Z 16b|DSurf15a 01|DSurf15a 71|DSurf15a 81|DSurf15a VA|EBrowse 1\.4b|Educate Search VxB|EmailSiphon|EmailWolf 1\.00|ESurf15a 15|ExtractorPro|Franklin Locator 1\.8|FSurf15a 01|Full Web Bot 0416B|Full Web Bot 0516B|Full Web Bot 2816B|Industry Program 1\.0\.x|ISC Systems iRc Search 2\.1|IUPUI Research Bot v 1\.9a|LARBIN-EXPERIMENTAL \(efp@gmx\.net\)|LetsCrawl\.com/1\.0 +http\://letscrawl\.com/|Lincoln State Web Browser|LWP\:\:Simple/5\.803|Mac Finder 1\.0\.xx|MFC Foundation Class Library 4\.0|Microsoft URL Control - 6\.00\.8xxx|Missauga Locate 1\.0\.0|Missigua Locator 1\.9|Missouri College Browse|Mizzu Labs 2\.2|Mo College 1\.9|Mozilla/2\.0 \(compatible; NEWT ActiveX; Win32\)|Mozilla/3\.0 \(compatible; Indy Library\)|Mozilla/4\.0 \(compatible; Advanced Email Extractor v2\.xx\)|Mozilla/4\.0 \(compatible; Iplexx Spider/1\.0 http\://www\.iplexx\.at\)|Mozilla/4\.0 \(compatible; MSIE 5\.0; Windows NT; DigExt; DTS Agent|Mozilla/4\.0 efp@gmx\.net|Mozilla/5\.0 \(Version\: xxxx Type\:xx\)|MVAClient|NASA Search 1\.0|Nsauditor/1\.x|PBrowse 1\.4b|PEval 1\.4b|Poirot|Port Huron Labs|Production Bot 0116B|Production Bot 2016B|Production Bot DOT 3016B|Program Shareware 1\.0\.2|PSurf15a 11|PSurf15a 51|PSurf15a VA|psycheclone|RSurf15a 41|RSurf15a 51|RSurf15a 81|searchbot admin@google\.com|sogou spider|sohu agent|SSurf15a 11 |TSurf15a 11|Under the Rainbow 2\.2|User-Agent\: Mozilla/4\.0 \(compatible; MSIE 6\.0; Windows NT 5\.1\)|WebVulnCrawl\.blogspot\.com/1\.0 libwww-perl/5\.803|Wells Search II|WEP Search 00|EmailCollector|WebEMailExtrac|TrackBack/1\.02|sogou music spider|AhrefsBot|SeznamBot|SemrushBot|PetalBot|).*$
`-

Ignoreregex: 0 total

Date template hits:

Lines: 17168 lines, 0 ignored, 160 matched, 17008 missed
[processed in 1.82 sec]

Missed line(s): too many to print.  Use --print-all-missed to print all 17008 lines
```

Manually search a file for certain keywords:

```
cat /var/logDocker/de5c71e9daaa/de5c71e9daaa-json.log | grep apikey.php
```

Show list Banded

```
fail2ban-client status
fail2ban-client status nginx-access-limit
```
