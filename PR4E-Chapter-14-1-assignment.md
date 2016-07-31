---
title: PR4E Chapter 14.1（Basic Structured Query Language）作业
date: 2016-07-25 21:54:44
tags:
- Python
- coursera
---
前天开始听的，听了五分钟，不懂，感觉像天书，刚好网络不行，就放弃了。昨天想，不听不行啊，还是得继续，结果听完大发感慨，怎么这么简单！quiz简单，第一道作业题也很简单，一路绿灯到第二道作业题，卡住了。折腾半晚上，看不懂，后悔自己太嘚瑟，今天继续折腾，大半个下午，简直要怀疑智商。最后，突然发现，代码没错，是数据拿错了……
就当时复习了正则表达式和list.split()。
另，终于能懂github的好了，虽然写作业的时候没用，但碰到project一定要用啊！

<!--more-->

# 知识点
## Database Introduction
### Relational Databases 关系数据库
Relational databases model data by storing rows and columns in tables. The power of the relational database lies in its ability to efficiently retrieve data from those tables and in particular where there are multiple tables and the relationships between those tables involved in the query.
其实并没有看懂……
### Terminology
- Database - contains many tables
- Relation (or table) - contains tuples and attributes
- Tuple (or row) - a set of fields that generally represents an “object” like a person or a music track
- Attribute (also column or field) - one of possibly many elements of data corresponding to the object represented by the row

Python + SQL的组合之强大在于
Python cleans up the data while SQL stores and retrieves data.

SQL has 4 basic functions -- CRUD
- Create
- Read
- Update
- Delete

## Using Databases
### Two Roles in Large Projects
- Application Developer - Builds the logic for the application, the look
and feel of the application - monitors the application for problems
- Database Administrator - Monitors and adjusts the database as the
program runs in production
- Often both people participate in the building of the “Data model”
### Database Model
A _database model_ or _database schema_ is the _structure or format of a database_, described in a formal language
supported by the database management system. In other
words, a “database model” is the application of a data
model when used in conjunction with a database
management system.
### Common Database Systems
Three major DMS:
- Oracle - Large, commercial, enterprise-scale, very very tweakable
- MySql - Simpler but very fast and scalable - commercial open source
- SqlServer - Very nice - from Microsoft (also Access)

## Single Table CRUD
### Insert
INSERT INTO Users (name, email) VALUES ('Kristin', 'kf@umich.edu')
### Delete
DELETE FROM Users WHERE email='ted@umich.edu'
###  Update
UPDATE Users SET name='Charles' WHERE email='csev@umich.edu'
### Retrieving Records: Select
SELECT * FROM Users
SELECT * FROM Users WHERE email='csev@umich.edu'
### Sorting with ORDER BY
SELECT * FROM Users ORDER BY email
SELECT * FROM Users ORDER BY name
# 题目
## 习题A
按照给定的指令在本地用sqlitebrowser操作，主要是create、insert这两个指令，好傻瓜的……我就不写了
最后有个组合指令有意思，但是不懂什么意思……
`SELECT hex(name || age) AS X FROM Ages ORDER BY X`
## 习题B
**Counting Organizations**

This application will read the mailbox data (mbox.txt) count up the number email messages per organization (i.e. domain name of the email address) using a database with the following schema to maintain the counts.

# 答案
## 习题B
### 正确答案
```
import sqlite3
import re

conn = sqlite3.connect('email07281.sqlite')
cur = conn.cursor()

cur.execute('''
DROP TABLE IF EXISTS Counts''')

cur.execute('''
CREATE TABLE Counts (org TEXT, count INTEGER)''')

fname = raw_input('Enter file name: ')
if ( len(fname) < 1 ) : fname = 'mbox.txt'
fh = open(fname)
for line in fh:
    if not line.startswith('From: ') : continue
    pieces = line.split('@') #sample中是pieces = line.split()
    org = pieces[1]
    org = org.rstrip()#remove the blank spaces
    print org

    cur.execute('SELECT count FROM Counts WHERE org = ? ', (org, ))
    row = cur.fetchone()
    if row is None:
        cur.execute('''INSERT INTO Counts (org, count)
                VALUES ( ?, 1 )''', ( org, ) )
    else:
        cur.execute('UPDATE Counts SET count=count+1 WHERE org = ?',(org, ))
        # This statement commits outstanding changes to disk each
        # time through the loop - the program can be made faster
        # by moving the commit so it runs only after the loop completes
    conn.commit()

sqlstr = 'SELECT org, count FROM Counts ORDER BY count DESC LIMIT 10'

print "Counts:"
for row in cur.execute(sqlstr) :
    print str(row[0]), row[1]

cur.close()
```
### 错误答案
其他部分都是相同的，奇怪的是，开始用小数据运行是好的呀。
```
for line in fh:
    if not line.startswith('From: ') : continue
    line = line.rstrip()
    email = re.findall('[a-zA-Z0-9]\S*@\S*[a-zA-Z]',line)
    #if len(email)>0:
        #print email
    for mail in email:
        a = mail.find('@')
        b = mail.find(' ',a)
        org = mail[a+1:b+1]
        print org
```
# 备注
## 习题B
其实第二题也是根据给定的代码修改的，最终成果是只改了一行，结果，为了这一行，折腾了四五个小时。
不过，要说明一下，这行代码不是我写的。参考：http://blog.csdn.net/suiqiji206/article/details/50498928
`pieces = line.split('@')`以前我都不知道这种分发……

但是，我最开始写的代码应该也没错，然而多次修改之后已经不记得最初的是什么了，所以说终于能理解为什么程序员们都爱github。
第一次得到的结果不对，我以为是理解错了题意，可能是要找出所有的email地址再从中找domain，而不是仅仅是“From”开头那行的。为此我还启用了正则表达式！写了长长的一串，见上面。
然而继续错继续错，百思不得其解。
偶然一下，发现是题目中提供的数据量太大，之前都是没有加载完就copy了，所以本地的数据是不完整的……于是重新弄了一遍，答案就正确了……

## 其他
Atom有点奇怪，每次修改了都得保存一下才能运行，TextWrangler就不用，改过直接运行就ok。（想念TextWrangler，要不要装回来呢……）

本章的slide链接：<a>https://www.dr-chuck.net/pythonlearn/slides/EN_us/Py4Inf-14-Databases.pdf</a>
