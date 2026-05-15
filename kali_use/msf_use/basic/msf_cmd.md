Msfconsole
#start:
msfconsole -q

在 msfconsole 命令后加上 -h 参数，从而查看所有可用的选项。

在 MSF 命令提示符下输入“help”或“?”，即可查看所有可用命令的列表，以及每个命令的用途说明。

你可以发出back命令来退出当前上下文.

msfconsole 内置了一个迷你 Netcat 克隆，支持 SSL、代理、端口转发和文件传输。通过使用带有 IP 地址和端口号的 connect 命令，您可以从 msfconsole 连接到远程主机，就像使用 Netcat 或 Telnet 一样。
msf > connect 192.168.1.1 23
[*] Connected to 192.168.1.1:23
DD-WRT v24 std (c) 2008 NewMedia-NET GmbH
Release: 07/27/08 (SVN revision: 10011)
DD-WRT login:
您可以通过使用-h 参数查看所有其他选项。

info 命令将提供有关特定模块的详细信息，包括所有选项、目标和其他信息。在使用之前，请务必阅读模块描述，因为有些模块可能会产生意想不到的效果。
msf  exploit(ms09_050_smb2_negotiate_func_index) > info exploit/windows/smb/ms09_050_smb2_negotiate_func_index

       Name: Microsoft SRV2.SYS SMB Negotiate ProcessID Function Table Dereference
     Module: exploit/windows/smb/ms09_050_smb2_negotiate_func_index
    Version: 14774
   Platform: Windows
 Privileged: Yes
    License: Metasploit Framework License (BSD)
       Rank: Good
       ......
       
运行 irb 命令将带您进入一个实时的 Ruby 解释器 shell，您可以在其中发出命令并即时创建 Metasploit 脚本。
msf > irb
[*] Starting IRB shell...

>> puts "Hello, metasploit!"
Hello, metasploit!
=> nil
>> Framework::Version
=> "4.8.2-2014022601"

jobs是正在后台运行的模块。jobs 命令提供了列出和终止这些工作的能力。
msf > jobs -h
Usage: jobs [options]

Active job manipulation and interaction.

OPTIONS:

    -K        Terminate all running jobs.
    -h        Help banner.
    -i   Lists detailed information about a running job.
    -k   Terminate the specified job name.
    -l        List all running jobs.
    -v        Print more detailed info.  Use with -i and -l

kill命令将使用作业 ID 杀死任何正在运行的作业。
msf exploit(ms10_002_aurora) > kill 0
Stopping job: 0...

[*] Server stopped.

load命令从 Metasploit 的插件目录加载一个插件。参数作为键=值的形式传递给 shell。
loadpath命令将加载第三方模块树以便您可以将 Metasploit 指向您的 0-day 漏洞利用、编码器、有效负载等。
相反，unload命令卸载之前加载的插件并移除任何扩展命令。

resource命令运行可以通过 msfconsole 加载的资源（批处理）文件。
批处理文件可以大大加快测试和开发时间，并允许用户自动化许多任务。除了从 msfconsole 内部加载批处理文件外，它们还可以使用-r 标志在启动时传递。下面的简单示例创建一个批处理文件，在启动时显示 Metasploit 的版本号。
root@kali:~# echo version > version.rc
root@kali:~# msfconsole -r version.rc

 _                                                    _
/     /         __                         _   __  /_/ __
| |  / | _____               ___   _____ | | /   _
| | /| | | ___ |- -|   /    / __ | -__/ | || | || | |- -|
|_|   | | | _|__  | |_  / - __    | |    | | __/| |  | |_
      |/  |____/  ___/ / \___/   /     __|    |_  ___

Frustrated with proxy pivoting? Upgrade to layer-2 VPN pivoting with
Metasploit Pro -- type 'go_pro' to launch it now.

       =[ metasploit v4.8.2-2014021901 [core:4.8 api:1.0] ]
+ -- --=[ 1265 exploits - 695 auxiliary - 202 post ]
+ -- --=[ 330 payloads - 32 encoders - 8 nops      ]

[*] Processing version.rc for ERB directives.
resource (version.rc)> version
Framework: 4.8.2-2014022601
Console  : 4.8.2-2014022601.15168

Metasploit 中的 route 命令允许你通过一个会话（comm）来路由套接字，提供基本的 Pivoting 功能。要添加一条路由，你需要传递目标子网和网络掩码，后面跟着会话（comm）编号。
route -h

msfconsole 包含基于正则表达式的强大搜索功能。如果你对要查找的内容有一个大致的概念，你可以通过 search 进行搜索。在下面的输出中，正在搜索 MS Bulletin MS09-011。搜索功能将在模块名称、描述、参考等中查找此字符串。
search usermap_script
help search
search name:mysql
您可以使用platform来缩小搜索范围，以查找影响特定平台的模块。
search platform:aix
Using the type lets you filter by module type such as auxiliary, post, exploit, etc.
search type:post
search author:dookie
您可以将多个关键字组合在一起，以进一步缩小返回结果的范围。
search cve:2011 author:jduck platform:linux

session命令允许您列出、与生成的会话进行交互以及终止生成的会话。会话可以是 shell、Meterpreter 会话、VNC 等。
sessions -h
要列出任何活动会话，请将-l 选项传递给 sessions。
sf exploit(3proxy) > sessions -l

Active sessions
===============

  Id  Description    Tunnel
  --  -----------    ------
  1   Command shell  192.168.1.101:33191 -> 192.168.1.104:4444
要与给定的会话进行交互，您只需使用 -i 开关，后面跟着会话的 ID 号即可。
msf exploit(3proxy) > sessions -i 1
[*] Starting interaction with 1...

set命令集允许您为当前正在使用的模块配置框架选项和参数。
msf auxiliary(ms09_050_smb2_negotiate_func_index) > set RHOST 172.16.194.134
RHOST => 172.16.194.134
msf auxiliary(ms09_050_smb2_negotiate_func_index) > show options
Metasploit 还允许你在运行时设置一个编码器。这在利用开发中特别有用，因为你不完全确定哪种负载编码方法适用于给定的利用。
msf  exploit(ms09_050_smb2_negotiate_func_index) > show encoders

与 set 命令相反的，当然是 unset。unset 命令用于移除之前使用 set 命令配置的参数。你可以使用 unset all 命令移除所有已分配的变量。

为了在渗透测试中节省大量输入，你可以在 msfconsole 中设置全局变量。你可以使用 setg 命令来完成此操作。
在设置完不同的变量后，您可以运行save命令来保存当前的环境和设置。
msf > setg LHOST 192.168.1.101
LHOST => 192.168.1.101
msf > setg RHOSTS 192.168.1.0/24
RHOSTS => 192.168.1.0/24
msf > setg RHOST 192.168.1.136
RHOST => 192.168.1.136
msf > save
Saved configuration to: /root/.msf4/config

进入 msfconsole 提示符后输入 show 将显示 Metasploit 中的所有模块。
可以使用多个 show 命令，但你最常使用的是 show auxiliary、show exploits、show payloads、show encoders 和 show nops。
当你处于某个特定 exploit 的上下文中时，运行 show payloads 只会显示与该特定 exploit 兼容的 payload
msf  exploit(ms08_067_netapi) > show payloads

Compatible Payloads
===================

   Name                                             Disclosure Date  Rank    Description
   ----                                             ---------------  ----    -----------
   generic/custom                                                    normal  Custom Payload
   generic/debug_trap                                                normal  Generic x86 Debug Trap
   generic/shell_bind_tcp                                            normal  Generic Command Shell, Bind TCP Inline
...snip...
如果你已经选择了特定的模块，你可以使用 show options 命令来显示该特定模块可用和/或所需的设置。
msf exploit(ms08_067_netapi) > show options

Module options:

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   RHOST                     yes       The target address
   RPORT    445              yes       Set the SMB service port
   SMBPIPE  BROWSER          yes       The pipe name to use (BROWSER, SRVSVC)

Exploit target:

   Id  Name
   --  ----
   0   Automatic Targeting
如果您不确定某个操作系统是否容易受到特定漏洞利用的攻击，请在漏洞利用模块的上下文中运行 show targets 命令，以查看支持哪些目标。
msf  exploit(ms08_067_netapi) > show targets

Exploit targets:

   Id  Name
   --  ----
   0   Automatic Targeting
   1   Windows 2000 Universal
   10  Windows 2003 SP1 Japanese (NO NX)
   11  Windows 2003 SP2 English (NO NX)
   12  Windows 2003 SP2 English (NX)
...snip...
如果你希望进一步微调一个漏洞利用，你可以通过运行 show advanced 来查看更多高级选项。
msf exploit(ms08_067_netapi) > show advanced

Module advanced options:

   Name           : CHOST
   Current Setting:
   Description    : The local client address

   Name           : CPORT
   Current Setting:
   Description    : The local client port

...snip...
运行 show encoders 将显示 MSF 中可用的编码器列表。
msf > show encoders
发出 show nops 命令将显示 Metasploit 提供的 NOP 生成器。
msf > show nops

当你决定使用某个特定模块时，发出 use 命令来选择它。use 命令将你的上下文更改为特定模块，暴露特定类型的命令。注意在下面的输出中，之前设置的任何全局变量都已配置。
msf > use dos/windows/smb/ms09_001_write
msf auxiliary(ms09_001_write) > show options

Module options:

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   RHOST                   yes       The target address
   RPORT  445              yes       Set the SMB service port

msf auxiliary(ms09_001_write) >
