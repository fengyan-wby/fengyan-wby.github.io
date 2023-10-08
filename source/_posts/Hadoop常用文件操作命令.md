---
title: Hadoop常用文件操作命令
date: 2023-03-14 15:30:58
categories: 工程
tags: ["Hadoop"]
---

介绍Hadoop的一些简单用法。
<!-- more -->

## 命令基本格式

```bash
hadoop fs -cmd < args >
```

## 展示

### ls

列出hdfs文件系统根目录下的目录和文件

```bash
hadoop fs -ls  /
```

## 上传本地文件到服务器

### put

```bash
hadoop fs -put < local file > < hdfs file >
```

### moveFromLocal

与put相类似，命令执行后源文件 local src 被删除

```bash
hadoop fs -moveFromLocal  < local src > ... < hdfs dst >
```

### copyFromLocal

```bash
hadoop fs -copyFromLocal  < local src > ... < hdfs dst >
```

## 从服务器下载文件到本地

### get

```bash
hadoop fs -get < hdfs file > < local file or dir>
```

### copyToLocal

```bash
hadoop fs -copyToLocal < local src > ... < hdfs dst >
```

### getmerge

```bash
# 将hdfs指定目录下所有文件排序后合并到local指定的文件中，文件不存在时会自动创建，文件存在时会覆盖里面的内容
hadoop fs -getmerge < hdfs dir >  < local file >
```

## 删除文件

### rm

```bash
hadoop fs -rm < hdfs file > ...
hadoop fs -rm -r < hdfs dir>...
```

## 创建目录

### mkdir

```bash
# 只能一级一级的建目录，父目录不存在的话使用这个命令会报错
hadoop fs -mkdir < hdfs path>

# 所创建的目录如果父目录不存在就创建该父目录
hadoop fs -mkdir -p < hdfs path> 
```

## 复制文件

### cp

```bash
hadoop fs -cp  < hdfs file >  < hdfs file >
```

## 移动文件

### mv

```bash
hadoop fs -mv < hdfs file >  < hdfs file >
```

## 统计

### count

```bash
# 统计hdfs对应路径下的目录个数，文件个数，文件总计大小
hadoop fs -count < hdfs path >
```

### du

```bash
# 显示hdfs对应路径下每个文件夹和文件的大小
hadoop fs -du < hdsf path> 
```
