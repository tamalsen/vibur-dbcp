**Used configuration properties: logConnectionLongerThanMs = 5000,  logStackTraceForLongConnection = true.**

```
2014-02-07 18:27:44,109 [misc_Worker-5] [] WARN  org.vibur.dbcp.ViburDBCPDataSource - 
Call to "getConnection()" took 8305 ms
  at org.springframework.orm.hibernate3.LocalDataSourceConnectionProvider.getConnection(LocalDataSourceConnectionProvider.java:83)
  at org.hibernate.jdbc.ConnectionManager.openConnection(ConnectionManager.java:446)
  at org.hibernate.jdbc.ConnectionManager.getConnection(ConnectionManager.java:167)
  at org.hibernate.jdbc.JDBCContext.connection(JDBCContext.java:160)
  at org.hibernate.transaction.JDBCTransaction.begin(JDBCTransaction.java:81)
  at org.springframework.orm.hibernate3.HibernateTransactionManager.doBegin(HibernateTransactionManager.java:551)
  at org.springframework.transaction.support.AbstractPlatformTransactionManager.getTransaction(AbstractPlatformTransactionManager.java:371)
  at com.some.package.SomeClass.method(SomeClass.java:92)
...............

2014-02-07 18:27:44,129 [misc_Worker-3] [] WARN  org.vibur.dbcp.ViburDBCPDataSource - 
Call to "getConnection()" took 7014 ms
  at org.springframework.orm.hibernate3.LocalDataSourceConnectionProvider.getConnection(LocalDataSourceConnectionProvider.java:83)
  at org.hibernate.jdbc.ConnectionManager.openConnection(ConnectionManager.java:446)
  at org.hibernate.jdbc.ConnectionManager.getConnection(ConnectionManager.java:167)
  at org.hibernate.jdbc.JDBCContext.connection(JDBCContext.java:160)
  at org.hibernate.transaction.JDBCTransaction.begin(JDBCTransaction.java:81)
  at org.springframework.orm.hibernate3.HibernateTransactionManager.doBegin(HibernateTransactionManager.java:551)
  at org.springframework.transaction.support.AbstractPlatformTransactionManager.getTransaction(AbstractPlatformTransactionManager.java:371)
  at com.some.package.AnotherClass.method(AnotherClass.java:77)
...............
```