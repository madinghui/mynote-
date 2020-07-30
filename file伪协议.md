---
title: file伪协议 
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
 7. glob:// — 查找匹配的文件路径模式
 8. phar:// — PHP 归档
 9. ssh2:// — Secure Shell 2
 10. rar:// — RAR
 11. ogg:// — 音频流
 12. expect:// — 处理交互式的流

### file://协议
file://用于访问本地的文件系统，通常用来读取本地文件而不受**allow_url_fopen**与**allow_url_include**的控制。

