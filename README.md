# Levivan.github.io
学习记录
1.登录
随便登入
bp抓包（输入账号密码后）
intruder爆破寻找长度不一样的
repeater go
2.php代码
弱类型比较
$a==$b等于
$a===$b全等
3.robot
url后输入/robots.txt
4.ping没写waf
command1 && command2 先执行 command1，如果为真，再执行command2 
command1 | command2   只执行 command2 
command1 & command2   先执行command2后执行command1 
command1 || command2  先执行 command1，如果为假，再执行command2 
命令执行漏洞（| || & && 称为 管道符） 
127.0.0.1&&ls
./返回上一级目录
cat命令127.0.0.1 && cat ../../../../home/flag.txt
5.请求头
php尝试index.php,F12查看network看请求头
或者bp抓包送到repeater
6.伪造xff（X-Forwarded-For）和referer
插件改请求头或者bp抓包改请求头和ip
7.常见的备份文件后缀名：.git .svn .swp .~ .bak .bash_history加在路由后面
8.URI/目录（index.php）
9.get请求以？为分割符，以&为并列请求
  post请求 requests.post(url,data)
10.js
出现以逗号隔开的数字
python将其翻译为ASCII码
s=[55,56,54,79,115,69,114,116,107,49,50]
for i in s:
print(chr(i),end=")
