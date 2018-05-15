---
layout: post
title: data_sanity - check your data's sanity
date: 2012-06-26 18:15:03.000000000 +08:00
tags:
- opensource
- ruby
- rubygem
---
*Background:* We are trying to rebuild an old application, earlier written as PL/SQL stored procedures, and now an RoR web application. The application having some highly sensitive data which has to be migrated to be used with the new one. Now the old system was pretty basic and usually errors where analysed manually in data and modified straight to DB whereas the new system being highly customized, and has handled all errors on addition of new records by throwing validation messages. Now the data migrated if doesn't holds all the validation this system imposes can lead to unknown errors.

*Solution:* A script or background process to go through all the data and point out such issues. This is a common situation when an application changes tech stack, goes through an update, etc. And so we thought of a reusable solution, data_sanity.

### data_sanity, the gem
 A simple gem, which you can plugin and run through your data and log the validation defaulters. It is pretty customized to handle various cases. Following are the use cases

* *Output options* - You can log validation defaulters in either a ``table`` in your db or a simple ``csv`` file

* *Table Scan* - You can scan through all the tables in your database or specify the tables you want to choose

* *Data scan* - You can scan through all the data or conditionally via data set. eg. Suppose you have a model Vehicle, which has a column type, which can have three values (FourWheeler, ThreeWheeler, TwoWheeler). In all there are 40,000 records in the table. And I want to test only 100 records of each type. I can specify the scanner to scan through 100 records of each type. (shown below)

Criteria in data_sanity_criteria.yml:
```yaml	
Vehicle:
  Type: ['FourWheeler', 'ThreeWheeler, 'TwoWheeler']
```

Rake Task: `rake data_sanity:investigate[csv,random,100]`
