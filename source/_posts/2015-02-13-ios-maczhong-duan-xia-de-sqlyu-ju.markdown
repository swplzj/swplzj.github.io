---
layout: post
title: " iOS mac终端下的SQL语句"
date: 2014-01-18 22:18:47 +0800
comments: true
categories: 
---

我们都知道数据库的创建可以借助图形化的数据库工具软件，但也可以在Mac终端下来直接使用代码创建。
下面介绍怎样建立数据库、建立资料表、建立索引、数据的增、删、改、查以及sqlite3命令列选项等。

<!--more-->

##### 建立数据库档案


打开终端，在终端用sqlite3建立数据库，在shell下键入（以下$符号为shell提示号，请勿键入）：

	$ sqlite3 foo.db
	
如果目录下没有foo.db，sqlite3就会建立这个数据库。

sqlite3并没有强制数据库档名要怎么取，因此如果你喜欢，也可以取个例如foo.icannameitwhateverilike的档名。

在sqlite3提示列下操作
进入了sqlite3之后，会看到以下文字：

```
SQLite version 3.1.3
Enter ".help" for instructions
sqlite> 
```	
	
这时如果使用.help可以取得求助，.quit则是离开（请注意：不是quit）

##### SQL的指令格式

所有的SQL指令都是以分号（;）结尾的。如果遇到两个减号（--）则代表注解，sqlite3会略过去。

#### 建立资料表

假设我们要建一个名叫film的资料表，只要键入以下指令就可以了：

	create table film(title, length, year, starring);
	
这样我们就建立了一个名叫film的资料表，里面有name、length、year、starring四个字段。
这个create table指令的语法为：

	create table table_name(field1, field2, field3, ...);
	
table_name是资料表的名称，fieldx则是字段的名字。

sqlite3与许多SQL数据库软件不同的是，它不在乎字段属于哪一种资料型态：sqlite3的字段可以储存任何东西：文字、数字、大量文字（blub），它会在适时自动转换。

#### 建立索引

如果资料表有相当多的资料，我们便会建立索引来加快速度。好比说：

	create index film_title_index on film(title);

意思是针对film资料表的name字段，建立一个名叫film_name_index的索引。这个指令的语法为

	create index index_name on table_name(field_to_be_indexed);

一旦建立了索引，sqlite3会在针对该字段作查询时，自动使用该索引。这一切的操作都是在幕后自动发生的，无须使用者特别指令。

#### 增加数据

接下来我们要插入数据了，加入的方法为使用insert into指令，语法为：

	insert into table_name values(data1, data2, data3, ...);
	
例如我们可以加入

```
insert into film values ('Silence of the Lambs, The', 118, 1991, 'Jodie Foster');
insert into film values ('Contact', 153, 1997, 'Jodie Foster');
insert into film values ('Crouching Tiger, Hidden Dragon', 120, 2000, 'Yun-Fat Chow');
insert into film values ('Hours, The', 114, 2002, 'Nicole Kidman');
```

如果该字段没有资料，我们可以填NULL。

#### 删除数据

delete from film where year < 1970;
就会删除所有年代早于1970年（不含）的电影了。

#### 更改数据

了解select的用法非常重要，因为要在sqlite更改或删除一笔资料，也是靠同样的语法。
例如有一笔资料的名字打错了：

	update film set starring='Jodie Foster' where starring='Jodee Foster';
	
就会把主角字段里，被打成'Jodee Foster'的那笔（或多笔）资料，改回成Jodie Foster。

#### 查询数据

讲到这里，我们终于要开始介绍SQL最强大的select指令了。我们首先简单介绍select的基本句型：
	
	select columns from table_name where expression;
	
最常见的用法，当然是倒出所有数据库的内容：

	select * from film;
	
如果资料太多了，我们或许会想限制笔数：
	
	select * from film limit 10;
	
或是照着电影年份来排列：
	
	select * from film order by year limit 10;
	
或是年份比较近的电影先列出来：

	select * from film order by year desc limit 10;
	
或是我们只想看电影名称跟年份：
	
	select title, year from film order by year desc limit 10;
	
查所有茱蒂佛斯特演过的电影：
	
	select * from film where starring='Jodie Foster';
	
查所有演员名字开头叫茱蒂的电影('%' 符号便是 SQL 的万用字符）：
	
	select * from film where starring like 'Jodie%';
	
查所有演员名字以茱蒂开头、年份晚于1985年、年份晚的优先列出、最多十笔，只列出电影名称和年份：
	
	select title, year from film where starring like 'Jodie%' and year >= 1985 order by year desc limit 10;
	
有时候我们只想知道数据库一共有多少笔资料：

	select count(*) from film;
	
有时候我们只想知道1985年以后的电影有几部：

	select count(*) from film where year >= 1985;


其他sqlite的特别用法

sqlite可以在shell底下直接执行命令：

	sqlite3 film.db "select * from film;"
	
输出 HTML 表格：
	
	sqlite3 -html film.db "select * from film;"
	
将数据库「导出来」：
	
	sqlite3 film.db ".dump" > output.sql
	
利用输出的资料，建立一个一模一样的数据库（加上以上指令，就是标准的SQL数据库备份了）：
	
	sqlite3 film.db < output.sql
	
在大量插入资料时，你可能会需要先打这个指令：

	begin;

插入完资料后要记得打这个指令，资料才会写进数据库中：

	commit;