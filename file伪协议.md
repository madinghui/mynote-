---
title: php伪协议 
tags: php学习
renderNumberedHeading: true
grammar_cjkRuby: true
---
### php中包含的伪协议
 

 1. file:// — 访问本地文件系统
 2. http:// — 访问 HTTP(s) 网址
 3. ftp:// — 访问 FTP(s) URLs
 4. php:// — 访问各个输入/输出流（I/O streams）
 5. zlib:// — 压缩流
 6. data:// — 数据（RFC 2397）
 

### file://协议
file://用于访问本地的文件系统，通常用来读取本地文件而不受**allow_url_fopen**与**allow_url_include**的控制。

### php://协议
==php:filter #E91E63==在双off的情况下也可以使用：
**不需要开启allow_url_fopen，仅php://input、 php://stdin、 php://memory 和 php://temp 需要开启allow_url_include**
php://访问各个输入和输出流，经常使用的是php://filter和php://input ,php://filter用来读取源码，php://input 用来执行代码。
### php://filter
==php://filter #E91E63== 是一种元封装器， 设计用于数据流打开时的筛选过滤应用。 这对于一体式（all-in-one）的文件函数非常有用，类似 readfile()、 file() 和 file_get_contents()， 在数据流内容读取之前没有机会应用其他过滤器。

 

``` nix
1. resource=<要过滤的数据流>     这个参数是必须的。它指定了你要筛选过滤的数据流。
 2. read=<读链的筛选列表>         该参数可选。可以设定一个或多个过滤器名称，以管道符（|）分隔。
 3. write=<写链的筛选列表>    该参数可选。可以设定一个或多个过滤器名称，以管道符（|）分隔。
 4. <；两个链的筛选列表>        任何没有以 read= 或 write= 作前缀 的筛选器列表会视情况应用于读或写链。
可以运用多种过滤器（字符串，转换，压缩，加密）
```

``` maxima
php://filter/read=convert.base64-encode/resource=upload.php
这里读的过滤器为convert.base64-encode，就和字面上的意思一样，把输入流base64-encode。
resource=upload.php，代表读取upload.php的内容
```
### 过滤器
过滤器有很多种，字符串过滤器，转换过滤器，压缩过滤器，加密过滤器
**字符串过滤器：**

``` stylus
string.rot13
进行rot13转换
string.toupper
将字符全部大写
string.tolower
将字符全部小写
string.strip_tags
去除空字符、HTML 和 PHP 标记后的结果。
功能类似于strip_tags()函数，若不想某些字符不被消除，后面跟上字符，可利用字符串或是数组两种方式。
```

``` php
<?php
    $fp = fopen('php://output', 'w');
    stream_filter_append($fp, 'string.rot13');
    echo "rot13:";
    fwrite($fp, "This is a test.\n");
    fclose($fp);
    echo "<br>";

    $fp = fopen('php://output', 'w');
    stream_filter_append($fp, 'string.toupper');
    echo "Upper:";
    fwrite($fp, "This is a test.\n");
    fclose($fp);
    echo "<br>";

    $fp = fopen('php://output', 'w');
    stream_filter_append($fp, 'string.tolower');
    echo "Lower:";
    fwrite($fp, "This is a test.\n");
    fclose($fp);
    echo "<br>";

    $fp = fopen('php://output', 'w');
    echo "Del1:";
    stream_filter_append($fp, 'string.strip_tags', STREAM_FILTER_WRITE);
    fwrite($fp, "<b>This is a test.</b>!!!!<h1>~~~~</h1>\n");
    fclose($fp);
    echo "<br>";

    $fp = fopen('php://output', 'w');
    echo "Del2:";
    stream_filter_append($fp, 'string.strip_tags', STREAM_FILTER_WRITE, "<b>");
    fwrite($fp, "<b>This is a test.</b>!!!!<h1>~~~~</h1>\n");
    fclose($fp);
    echo "<br>";

    $fp = fopen('php://output', 'w');
    stream_filter_append($fp, 'string.strip_tags', STREAM_FILTER_WRITE, array('b','h1'));
    echo "Del3:";
    fwrite($fp, "<b>This is a test.</b>!!!!<h1>~~~~</h1>\n");
    fclose($fp);
?>
```
<转换过滤器>
![转换](http://img/小书匠/1596209773827.png)

``` php
<?php
    $fp = fopen('php://output', 'w');
    stream_filter_append($fp, 'convert.base64-encode');
    echo "base64-encode:";
    fwrite($fp, "This is a test.\n");
    fclose($fp);
    echo "<br>";

    $param = array('line-length' => 8, 'line-break-chars' => "\n");
    $fp = fopen('php://output', 'w');
    stream_filter_append($fp, 'convert.base64-encode', STREAM_FILTER_WRITE, $param);
    echo "\nbase64-encode-split:\n";
    fwrite($fp, "This is a test.\n");
    fclose($fp);
    echo "<br>";

    $fp = fopen('php://output', 'w');
    stream_filter_append($fp, 'convert.base64-decode');
    echo "\nbase64-decode:";
    fwrite($fp, "VGhpcyBpcyBhIHRlc3QuCg==\n");
    fclose($fp);
    echo "<br>";

    $fp = fopen('php://output', 'w');
    stream_filter_append($fp, 'convert.quoted-printable-encode');
    echo "quoted-printable-encode:";
    fwrite($fp, "This is a test.\n");
    fclose($fp);
    echo "<br>";

    $fp = fopen('php://output', 'w');
    stream_filter_append($fp, 'convert.quoted-printable-decode');
    echo "\nquoted-printable-decode:";
    fwrite($fp, "This is a test.=0A");
    fclose($fp);
    echo "<br>";

?>
```
PHP file_put_contents() 函数：
payload=http://127.0.0.1/xxx.php?a=php://filter/write=string.tolower/resource=test.php
可以往服务器中写入一个文件内容全为小写且文件名为test.php的文件

PHP file_get_contents() 函数
file_get_contents()的$filename参数不仅仅为文件路径，还可以是一个URL（伪协议）。
payload=http://127.0.0.1/xxx.php?a=php://filter/convert.base64-encode/resource=test.php
test.php的内容以base64编码的方式显示出来

双引号包含的变量$filename会被当作正常变量执行，而单引号包含的变量则会被当作字符串执行。

``` php
<?php  
$aa=1;
echo "$aa";
echo "<br>";
echo '$aa';
?>
```
## php://input
**php://input 是个可以访问请求的原始数据的只读流,可以读取到post没有解析的原始数据, 将post请求中的数据作为PHP代码执行。因为它不依赖于特定的 php.ini 指令。
注：enctype=”multipart/form-data” 的时候 php://input 是无效的。**

## php://output
是一个只写的数据流， 允许你以 print 和 echo 一样的方式 写入到输出缓冲区。

``` php
<?php  
$code=$_GET["a"];  
file_put_contents($code,"test");   
?>  
```
## data://
data:资源类型;编码,内容
数据流封装器
当allow_url_include 打开的时候，任意文件包含就会成为任意命令执行

``` php
<?php  
$filename=$_GET["a"];  
include("$filename");  
?>  
```

``` livecodeserver
http://127.0.0.1/xxx.php?a=data://text/plain,<?php phpinfo()?>
or
http://127.0.0.1/xxx.php?a=data://text/plain;base64,PD9waHAgcGhwaW5mbygpPz4=

或者
http://127.0.0.1/cmd.php?file=data:text/plain,<?php phpinfo()?>
or
http://127.0.0.1/cmd.php?file=data:text/plain;base64,PD9waHAgcGhwaW5mbygpPz4=
```

 ## zip://, bzip2://, zlib://协议
 
 

``` livecodeserver
PHP.ini：
zip://, bzip2://, zlib://协议在双off的情况下也可以正常使用；
allow_url_fopen ：off/on
allow_url_include：off/on
```

==3 个封装协议，都是直接打开压缩文件。
compress.zlib://file.gz - 处理的是 '.gz' 后缀的压缩包
compress.bzip2://file.bz2 - 处理的是 '.bz2' 后缀的压缩包
zip://archive.zip#dir/file.txt - 处理的是 '.zip' 后缀的压缩包里的文件==

## zip://协议
使用方法：
zip://archive.zip#dir/file.txt

zip:// [压缩文件绝对路径]#[压缩文件内的子文件名]**
要用绝对路径+url编码#

## bzip2://协议

使用方法：
compress.bzip2://file.bz2
相对路径也可以

![enter description here](http://img/小书匠/1596642287558.png)
