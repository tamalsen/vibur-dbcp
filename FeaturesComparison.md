<a href='Hidden comment: 
Copyright 2014 Simeon Malchev

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
'></a>

### Features comparison of [Vibur DBCP](https://code.google.com/p/vibur-dbcp/), Apache Commons DBCP and C3P0 ###

This is an edited and up-to-date version of a post which first appeared on [coderanch](http://www.coderanch.com/t/623874/ba/Vibur-DBCbP-Released#2858629).

#### Guarantee that no thread will be starved out from accessing the pool's resources: ####
  * Vibur DBCP provides such support via using a standard Java Semaphore.
  * Apache Commons DBCP provides such support via custom implementation in Apache Commons Pool.
  * C3P0 - seems there is no support.

#### Detection and logging of long running SQL queries and long lasting getConnection() method calls: ####
  * Vibur DBCP provides support for both of these. A long running SQL queries log sample can be seen [here](SQLQueriesLogSample.md) and a long lasting getConnection() calls log sample is shown [here](getConnectionLogSample.md).
  * Apache Commons DBCP and C3P0 - seems there is no support.

#### Hibernate 3.x and 4.x integration support: ####
  * Vibur DBCP comes with [Hibernate3](https://code.google.com/p/vibur-dbcp-hibernate3/) and [Hibernate4](https://code.google.com/p/vibur-dbcp-hibernate4/) integrations.
  * Apache Commons DBCP does not have built-in support for Hibernate, however a class providing integration for Hibernate 3.x is publicly available.
  * C3P0 has integration for Hibernate 3.x and 4.x as part of the Hibernate distributions.

#### Built using standard Java concurrency utilities: ####
  * Vibur DBCP does not use any synchronized blocks/methods during normal pool operation. At low level, the object pooling is built on top of a standard ConcurrentLinkedQueue which is guarded by a standard Semaphore, and a ConcurrentLinkedHashMap is utilized for JDBC Statement caching purposes.
  * Both Apache Commons DBCP and C3P0 make heavy use of synchronized blocks/methods as part of their pools operations.

#### Source code size: ####
  * Having as simple as possible overall design and concise and easy to maintain source code was one of the main objectives of Vibur DBCP. Currently it has approximately 30 source files and about 3.3K SLOC total.
  * Apache Commons DBCP includes over 75 source files and C3P0 has over 90 source files. Both of them have considerably larger SLOC total than Vibur DBCP.

#### Built on top of a separate and dedicated object pool project: ####
  * Vibur DBCP is built on top of [Vibur Object Pool](https://code.google.com/p/vibur-object-pool/)
  * Apache Commons DBCP is built on top of Apache Commons Pool.
  * For C3P0 the object pooling implementation is part of the C3P0 source code.

#### JDBC Connection validation: ####
  * Vibur DBCP validates the JDBC connections on take if they have been idle for longer than a configured time, and implements this via calling the standard Connection.isValid() method or via executing a configured custom query. Can be configured to validate the connections on every take.
  * Both Apache Commons DBCP and C3P0 can validate the JDBC connections on take, restore or while idle via executing a configured custom query.

#### Eviction of idle JDBC Connections: ####
  * Vibur DBCP supports this via configuring reducerTimeIntervalInSeconds and reducerSamples.
  * Apache Commons DBCP supports this via configuring timeBetweenEvictionRunsMillis, numTestsPerEvictionRun and minEvictableIdleTimeMillis.
  * C3P0 supports this via configuring maxConnectionAge, maxIdleTime and maxIdleTimeExcessConnections.

#### Caching support for JDBC Statements (Prepared and Callable): ####
  * Implemented in one or another way by all pools.

#### Access to the raw (underlying) JDBC Connection: ####
  * Implemented in one or another way by all pools.

#### Providing records (including stack traces) for all currently taken (unclosed) JDBC connections: ####
  * Implemented in one or another way by all pools.