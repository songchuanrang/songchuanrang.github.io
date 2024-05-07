---
title: GC日志分析之GCViewer
date: 2022-4-3 20:37:25.000 +0800
type: book
weight: 50
---

## 源码下载

源码地址： https://github.com/chewiebug/GCViewer

## 编译

```bash
mvn clean package -DskipTests=true
```

## 运行

```bash
java -jar gcviewer.jar gc.log
```

## 时间线解释(只需要勾选以下时间线)

- Tenured Generation：年老代
- Yong Generation：年轻代
- Used Heap：使用的堆大小
- Used Tenured Heap：使用的年老代堆大小
- Used Yong Heap：使用的年轻代堆大小

## gc 日志开启参数及解析

```bash
-XX:+PrintGCDetails -XX:+PrintGCDateStamps -Xloggc:/path/to/log -XX:+UseGCLogFileRotation -XX:NumberOfGCLogFiles=10 -XX:GCLogFileSize=100M
```

java 程序在启动时，可以通过添加以下参数输出 gc 日志

- -verbose:gc：打印 gc 信息
- -XX:+PrintGC：是以上参数的非稳定版本
- -Xloggc:/path/to/log：GC 日志存放的位置
- -XX:+PrintGCDetails：开启详细的 GC 日志模式（-XX:+PrintGC：简单模式）
- -XX:+PrintGCDateStamps：打印 GC 日期
- -XX:+PrintGCTimeStamps：打印每次 GC 的时间戳（现在距离启动的时间长度）
- -XX:+PrintHeapAtGC：在进行 GC 的前后打印出堆的信息
- -XX:+UseGCLogFileRotation：开启 GC Log 的滚动功能
- -XX:NumberOfGCLogFiles=10：滚动日志文件的个数
- -XX:GCLogFileSize=100M：滚动日志文件的大小
