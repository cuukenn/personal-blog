---
title: liquibase随记
date: 2022-07-22 12:00:00
tags: 
  - liquibase
  - java
categories: 
  - program
---

AbstractSqlStatement：修改表结构的抽象类，存在很多相关子类。

 Database database = DatabaseFactory.getInstance().findCorrectDatabaseImplementation(jdbcConnection);
 AddColumnStatement statement = new AddColumnStatement();
 ExecutorService.getInstance().getExecutor(database).execute(statement);
