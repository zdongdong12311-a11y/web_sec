# Service Identification
Scanning Services Using Metasploit
除了使用 Nmap 对目标网络上的服务进行扫描外，Metasploit 还包含各种服务的大量扫描器，通常可以帮助您确定目标机器上可能存在漏洞的运行服务。
我们将加载 ssh_version 辅助扫描器，并使用 set 命令设置“RHOSTS”选项。然后，我们只需输入 run 即可运行该模块。
msf > use auxiliary/scanner/ssh/ssh_version

msf  auxiliary(ssh_version) > set RHOSTS 172.16.194.163 172.16.194.172
RHOSTS => 172.16.194.163 172.16.194.172

msf  auxiliary(ssh_version) > show options

Module options (auxiliary/scanner/ssh/ssh_version):

   Name     Current Setting                Required  Description
   ----     ---------------                --------  -----------
   RHOSTS   172.16.194.163 172.16.194.172  yes       The target address range or CIDR identifier
   RPORT    22                             yes       The target port
   THREADS  1                              yes       The number of concurrent threads
   TIMEOUT  30                             yes       Timeout for the SSH probe


msf  auxiliary(ssh_version) > run

[*] 172.16.194.163:22, SSH server version: SSH-2.0-OpenSSH_5.3p1 Debian-3ubuntu7
[*] Scanned 1 of 2 hosts (050% complete)
[*] 172.16.194.172:22, SSH server version: SSH-2.0-OpenSSH_4.7p1 Debian-8ubuntu1
[*] Scanned 2 of 2 hosts (100% complete)
[*] Auxiliary module execution completed

FTP Service 
配置不当的 FTP 服务器经常可以成为你进入整个网络的立足点，因此在遇到开放的 FTP 端口时，检查是否允许匿名访问总是值得的，而开放的 FTP 端口通常位于 TCP 端口 21 上。我们将在这里将‘THREADS’设置为‘1’，因为我们只扫描一个主机。
msf > services -p 21 -c name,proto

Services
========

host            name  proto
----            ----  -----
172.16.194.172  ftp   tcp

msf > use auxiliary/scanner/ftp/ftp_version

msf  auxiliary(ftp_version) > set RHOSTS 172.16.194.172
RHOSTS => 172.16.194.172

msf  auxiliary(anonymous) > show options
Module options (auxiliary/scanner/ftp/anonymous):

   Name     Current Setting      Required  Description
   ----     ---------------      --------  -----------
   FTPPASS  mozilla@example.com  no        The password for the specified username
   FTPUSER  anonymous            no        The username to authenticate as
   RHOSTS   172.16.194.172       yes       The target address range or CIDR identifier
   RPORT    21                   yes       The target port
   THREADS  1                    yes       The number of concurrent threads

msf  auxiliary(anonymous) > run

[*] 172.16.194.172:21 Anonymous READ (220 (vsFTPd 2.3.4))
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed



