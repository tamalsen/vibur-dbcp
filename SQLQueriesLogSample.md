**Used configuration properties: logQueryExecutionLongerThanMs = 5000,  logStackTraceForLongQueryExecution = true.**

```
2014-02-07 18:28:25,641 [Thread-27] [] WARN  org.vibur.dbcp.proxy.StatementInvocationHandler - SQL query execution took 7589 ms
-- select top 1 this_.id as id31_0_, this_.asset_id as asset2_31_0_, 
this_.last_tried_time as last3_31_0_, this_.retries as retries31_0_ 
from RETRACTION_QUEUE this_ where this_.retries<=? 
-- Parameters:
-- [[setInt, 1, 5]]
  at com.sun.proxy.$Proxy70.executeQuery(Unknown Source)
  at org.hibernate.jdbc.AbstractBatcher.getResultSet(AbstractBatcher.java:208)
  at org.hibernate.loader.Loader.getResultSet(Loader.java:1953)
  at org.hibernate.loader.Loader.doQuery(Loader.java:802)
  at org.hibernate.loader.Loader.doQueryAndInitializeNonLazyCollections(Loader.java:274)
  at org.hibernate.loader.Loader.doList(Loader.java:2533)
  at org.hibernate.loader.Loader.listIgnoreQueryCache(Loader.java:2276)
  at org.hibernate.loader.Loader.list(Loader.java:2271)
  at org.hibernate.loader.criteria.CriteriaLoader.list(CriteriaLoader.java:119)
  at org.hibernate.impl.SessionImpl.list(SessionImpl.java:1716)
  at org.hibernate.impl.CriteriaImpl.list(CriteriaImpl.java:347)
  at org.hibernate.impl.CriteriaImpl.uniqueResult(CriteriaImpl.java:369)
  at com.some.package.AppClass.method(AppClass.java:30)
...............

2014-02-07 18:28:26,928 [Thread-198] [] WARN  org.vibur.dbcp.proxy.StatementInvocationHandler - SQL query execution took 8734 ms
-- select persistent0_.ID as ID0_, persistent0_.DESTALIAS as DESTALIAS0_, persistent0_.EXPIRY_TIME as EXPIRY3_0_, 
persistent0_.Identifier as Identifier0_, persistent0_.LAST_TRIED_TIME as LAST5_0_, persistent0_.Message as Message0_,
persistent0_.payload as payload0_, persistent0_.PRIORITY as PRIORITY0_, persistent0_.RETRIES as RETRIES0_, 
persistent0_.TIMESTAMP as TIMESTAMP0_ from ASYNC_SEND_MESSAGE persistent0_ where persistent0_.DESTALIAS=? and
(persistent0_.EXPIRY_TIME>persistent0_.LAST_TRIED_TIME or persistent0_.LAST_TRIED_TIME is null) 
order by persistent0_.TIMESTAMP ASC, persistent0_.PRIORITY DESC 
-- Parameters:
-- [[setString, 1, RIVER]]
  at com.sun.proxy.$Proxy70.executeQuery(Unknown Source)
  at org.hibernate.jdbc.AbstractBatcher.getResultSet(AbstractBatcher.java:208)
  at org.hibernate.loader.Loader.getResultSet(Loader.java:1953)
  at org.hibernate.loader.Loader.doQuery(Loader.java:802)
  at org.hibernate.loader.Loader.doQueryAndInitializeNonLazyCollections(Loader.java:274)
  at org.hibernate.loader.Loader.doList(Loader.java:2533)
  at org.hibernate.loader.Loader.listIgnoreQueryCache(Loader.java:2276)
  at org.hibernate.loader.Loader.list(Loader.java:2271)
  at org.hibernate.loader.hql.QueryLoader.list(QueryLoader.java:452)
  at org.hibernate.hql.ast.QueryTranslatorImpl.list(QueryTranslatorImpl.java:363)
  at org.hibernate.engine.query.HQLQueryPlan.performList(HQLQueryPlan.java:196)
  at org.hibernate.impl.SessionImpl.list(SessionImpl.java:1268)
  at org.hibernate.impl.QueryImpl.list(QueryImpl.java:102)
  at com.some.package.AnotherAppClass.method(AnotherAppClass.java:35)
...............
```