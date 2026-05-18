# SNMP Sweeping
SNMP Auxiliary Module for Metasploit
SNMP 扫描通常擅长发现特定系统的各种信息，甚至可能完全控制远程设备。例如，如果你能找到运行私有字符串的 Cisco 设备，你实际上可以下载整个设备的配置，修改它，并上传你自己的恶意配置。通常，密码本身是第 7 级编码的，这意味着它们很容易解码并获得特定设备的启用或登录密码。
Metasploit 自带了一个专门用于扫描 SNMP 设备的内置辅助模块。在进行 SNMP 扫描之前，有几点需要了解。首先，'只读'和'读写'社区字符串在可以提取或修改设备上的信息类型方面起着重要作用。如果你能“猜”到只读或读写字符串，你可以获得通常无法获得的大量访问权限。此外，如果基于 Windows 的设备配置了 SNMP，通常带有 RO/RW 社区字符串，你可以提取补丁级别、运行的服务、上次重启时间、系统上的用户名、路由以及攻击者有价值的各种其他信息。
默认情况下，Metasploitable 的 SNMP 服务仅监听本地主机。这里演示的许多示例将需要您更改这些默认设置。打开并编辑/etc/default/snmpd，并将以下内容更改为：
SNMPDOPTS='-Lsd -Lf /dev/null -u snmp -I -smux -p /var/run/snmpd.pid 127.0.0.1'
to  
SNMPDOPTS='-Lsd -Lf /dev/null -u snmp -I -smux -p /var/run/snmpd.pid 0.0.0.0'
需要重启服务才能使更改生效。重启后，您现在可以从攻击机器扫描该服务。

# mib
查询 SNMP 时，有一个称为 MIB API 的东西。MIB 代表管理信息库。这个接口允许您查询设备并提取信息。Metasploit 自带一个默认 MIB 列表，它会根据获取的访问级别使用这些 MIB 来查询设备以获取更多信息。让我们来看看辅助模块。
msf >  search snmp

Matching Modules
================

   Name                                               Disclosure Date  Rank    Description
   ----                                               ---------------  ----    -----------
   auxiliary/scanner/misc/oki_scanner                                  normal  OKI Printer Default Login Credential Scanner
   auxiliary/scanner/snmp/aix_version                                  normal  AIX SNMP Scanner Auxiliary Module
   auxiliary/scanner/snmp/cisco_config_tftp                            normal  Cisco IOS SNMP Configuration Grabber (TFTP)
   auxiliary/scanner/snmp/cisco_upload_file                            normal  Cisco IOS SNMP File Upload (TFTP)
   auxiliary/scanner/snmp/snmp_enum                                    normal  SNMP Enumeration Module
   auxiliary/scanner/snmp/snmp_enumshares                              normal  SNMP Windows SMB Share Enumeration
   auxiliary/scanner/snmp/snmp_enumusers                               normal  SNMP Windows Username Enumeration
   auxiliary/scanner/snmp/snmp_login                                   normal  SNMP Community Scanner
   auxiliary/scanner/snmp/snmp_set                                     normal  SNMP Set Module
   auxiliary/scanner/snmp/xerox_workcentre_enumusers                   normal  Xerox WorkCentre User Enumeration (SNMP)
   exploit/windows/ftp/oracle9i_xdb_ftp_unlock        2003-08-18       great   Oracle 9i XDB FTP UNLOCK Overflow (win32)
   exploit/windows/http/hp_nnm_ovwebsnmpsrv_main      2010-06-16       great   HP OpenView Network Node Manager ovwebsnmpsrv.exe main Buffer Overflow
   exploit/windows/http/hp_nnm_ovwebsnmpsrv_ovutil    2010-06-16       great   HP OpenView Network Node Manager ovwebsnmpsrv.exe ovutil Buffer Overflow
   exploit/windows/http/hp_nnm_ovwebsnmpsrv_uro       2010-06-08       great   HP OpenView Network Node Manager ovwebsnmpsrv.exe Unrecognized Option Buffer Overflow
   exploit/windows/http/hp_nnm_snmp                   2009-12-09       great   HP OpenView Network Node Manager Snmp.exe CGI Buffer Overflow
   exploit/windows/http/hp_nnm_snmpviewer_actapp      2010-05-11       great   HP OpenView Network Node Manager snmpviewer.exe Buffer Overflow
   post/windows/gather/enum_snmp                                       normal  Windows Gather SNMP Settings Enumeration (Registry)

msf >  use auxiliary/scanner/snmp/snmp_login
msf auxiliary(snmp_login) >  show options

Module options (auxiliary/scanner/snmp/snmp_login):

   Name              Current Setting                     Required  Description
   ----              ---------------                     --------  -----------
   BLANK_PASSWORDS   false                               no        Try blank passwords for all users
   BRUTEFORCE_SPEED  5                                   yes       How fast to bruteforce, from 0 to 5
   DB_ALL_CREDS      false                               no        Try each user/password couple stored in the current database
   DB_ALL_PASS       false                               no        Add all passwords in the current database to the list
   DB_ALL_USERS      false                               no        Add all users in the current database to the list
   PASSWORD                                              no        The password to test
   PASS_FILE         /usr/share/wordlists/fasttrack.txt  no        File containing communities, one per line
   RHOSTS                                                yes       The target address range or CIDR identifier
   RPORT             161                                 yes       The target port
   STOP_ON_SUCCESS   false                               yes       Stop guessing when a credential works for a host
   THREADS           1                                   yes       The number of concurrent threads
   USER_AS_PASS      false                               no        Try the username as the password for all users
   VERBOSE           true                                yes       Whether to print output for all attempts
   VERSION           1                                   yes       The SNMP version to scan (Accepted: 1, 2c, all)

msf auxiliary(snmp_login) >  set RHOSTS 192.168.0.0-192.168.5.255
rhosts => 192.168.0.0-192.168.5.255
msf auxiliary(snmp_login) >  set THREADS 10
threads => 10
msf auxiliary(snmp_login) >  run
[*] >> progress (192.168.0.0-192.168.0.255) 0/30208...
[*] >> progress (192.168.1.0-192.168.1.255) 0/30208...
[*] >> progress (192.168.2.0-192.168.2.255) 0/30208...
[*] >> progress (192.168.3.0-192.168.3.255) 0/30208...
[*] >> progress (192.168.4.0-192.168.4.255) 0/30208...
[*] >> progress (-) 0/0...
[*] 192.168.1.50 'public' 'APC Web/SNMP Management Card (MB:v3.8.6 PF:v3.5.5 PN:apc_hw02_aos_355.bin AF1:v3.5.5 AN1:apc_hw02_sumx_355.bin MN:AP9619 HR:A10 SN: NA0827001465 MD:07/01/2008) (Embedded PowerNet SNMP Agent SW v2.2 compatible)'
[*] Auxiliary module execution completed
可以看到，我们找到了一个“public”的社区字符串。这很可能是只读的，不会透露太多信息。我们得知该设备是 APC Web/SNMP 设备，以及它运行的版本。

SNMP Enum
当使用 SNMP 扫描模块（如开放端口、服务、主机名、进程和运行时间等）时，我们可以收集大量信息。使用我们的 Metasploitable 虚拟机作为目标，我们将运行 auxiliary/scanner/snmp/snmp_enum 模块并查看它将为我们提供什么信息。首先，我们加载该模块并使用存储在工作区中的信息设置“RHOST”选项。使用 hosts -R 将为我们设置此选项。
msf  auxiliary(snmp_enum) > run

[+] 172.16.194.172, Connected.

[*] System information:

Host IP                       : 172.16.194.172
Hostname                      : metasploitable
Description                   : Linux metasploitable 2.6.24-16-server #1 SMP Thu Apr 10 13:58:00 UTC 2008 i686
Contact                       : msfdev@metasploit.com
Location                      : Metasploit Lab
Uptime snmp                   : 02:35:38.71
Uptime system                 : 00:20:13.21
System date                   : 2012-7-9 18:11:11.0

[*] Network information:

IP forwarding enabled         : no
Default TTL                   : 64
TCP segments received         : 19
TCP segments sent             : 21
TCP segments retrans          : 0
Input datagrams               : 5055
Delivered datagrams           : 5050
Output datagrams              : 4527

...snip...

[*] Device information:

Id                  Type                Status              Descr
768                 Processor           unknown             GenuineIntel: Intel(R) Core(TM) i7-2860QM CPU @ 2.50GHz
1025                Network             unknown             network interface lo
1026                Network             unknown             network interface eth0
1552                Disk Storage        unknown             SCSI disk (/dev/sda)
3072                Coprocessor         unknown             Guessing that there's a floating point co-processor

[*] Processes:

Id                  Status              Name                Path                Parameters
1                   runnable            init                /sbin/init
2                   runnable            kthreadd            kthreadd
3                   runnable            migration/0         migration/0
4                   runnable            ksoftirqd/0         ksoftirqd/0
5                   runnable            watchdog/0          watchdog/0
6                   runnable            events/0            events/0
7                   runnable            khelper             khelper
41                  runnable            kblockd/0           kblockd/0
68                  runnable            kseriod             kseriod

...snip...

5696                runnable            su                  su
5697                runnable            bash                bash
5747                running             snmpd               snmpd


[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
上述由我们的 SNMP 扫描提供的输出为我们提供了有关目标系统的大量信息。
可以看到有关目标的许多相关信息，例如其处理器类型、进程 ID 等。
