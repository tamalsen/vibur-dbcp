<a href='Hidden comment: 
Copyright 2013 Simeon Malchev

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



<table cellpadding='0' border='0' cellspacing='0'><tr>
<td width='60%' align='left' valign='top'>
<b>Vibur DBCP</b> is a concurrent, <a href='#Some_Code_Metrics_and_Performance_Results.md'>fast</a> and fully-featured JDBC connection pool based on Java <a href='https://www.google.com/search?num=10&q=Java+dynamic+proxies'>dynamic proxies</a>. It supports a fairness parameter, statement caching, slow SQL queries logging, and Hibernate integration, among other features.<br>
<br>
The latest release is <b>1.2.0</b> (<a href='https://code.google.com/p/vibur-dbcp/source/browse/CHANGELOG'>Changelog</a>, Maven <a href='#Maven_coordinates_and_how_to_build:.md'>coordinates</a>).<br>
<br>
<br>
What makes Vibur DBCP different from most of its competitors are its <a href='#Some_Code_Metrics_and_Performance_Results.md'>concise</a> (about 3.3K SLOC total) and easy to maintain<br>
source code, and its modular design which includes a separate and dedicated <a href='https://code.google.com/p/vibur-object-pool/'>object pool</a>. Features comparison of Vibur DBCP with C3P0 and Apache Commons DBCP can be seen <a href='FeaturesComparison.md'>here</a>.<br>
<br>
Another advantage of being implemented with dynamic proxies is that Vibur DBCP source code does not have to implement (depend on) every single method of the concrete JDBC API spec. For example, the 6 main JDBC interfaces for which Vibur DBCP provides proxies have a total of  approx 480 methods, however the invocations of less than 100 of them are explicitly intercepted and handled, and all other methods are simply mapped to their default implementations.<br>
</td>

<td width='4%'></td>

<td width='36%' align='left' valign='top'>
<ol><li><a href='#Main_features_and_characteristics:.md'>Main Features and Characteristics</a>
<ul><li><a href='#Code_Metrics_and_Performance_Results.md'>Code Metrics and Performance Results</a>
</li></ul></li><li><a href='#Usage_and_Configuration.md'>Usage and Configuration</a>
<ul><li><a href='#Maven_coordinates_and_how_to_build:.md'>Maven Coordinates and How to Build</a>
</li><li><a href='#Hibernate_3.x/4.x_integration:.md'>Hibernate 3.x/4.x Integration and Configuration</a>
</li><li><a href='#Spring_with_Hibernate_3.x/4.x_configuration_snippet:.md'>Spring with Hibernate 3.x/4.x Configuration</a>
</li><li><a href='#Programming_configuration_snippet:.md'>Programming Configuration</a>
</li><li><a href='#Log4j_configuration_snippet:.md'>Log4j Configuration</a>
</li></ul></li><li><a href='#Configuration_Options.md'>Configuration Options</a>
</li><li><a href='#Development_Team.md'>Development Team</a>
</td>
</tr></table></li></ol>

#### Main features and characteristics: ####

  * Guarantee that no thread will be starved out from accessing the pool's underlying resources. See the [poolFair](#Pool_size,_growth_and_fairness_settings:.md) configuration parameter.
  * Detection and logging of long running SQL queries (including their concrete parameters), and long lasting getConnection() method calls. See the related configuration [properties](#Database_connection_and_SQL_queries_logging_settings:.md) and the logs samples for [slow SQL queries](SQLQueriesLogSample.md) and [getConnection()](getConnectionLogSample.md) calls.
  * Hibernate 3.x and 4.x integration support.
  * Caching support for JDBC Statements (Prepared and Callable).

  * Built using standard Java concurrency utilities and dynamic proxies. Does not use any synchronized blocks/methods during normal pool operations.
  * Vibur DBCP requires Java 1.6+ and only the following external dependencies: its dedicated [object pool](https://code.google.com/p/vibur-object-pool/), slf4j/log4j, and ConcurrentLinkedHashMap. The CLHM dependency can be excluded if the JDBC Statement caching is not used.

#### Other features: ####

  * Validation intervals [support](#Database_connection_validation_settings:.md), i.e. the taken from the pool connection is not validated before every use but is validated only if a given time has passed since the connection's last use.
  * Intelligent [pool sizing](#Pool_size_reduction_settings_(advanced):.md) - the number of idle connections in the pool can be reduced based on heuristics for the number of recently used connections.

  * The raw JDBC connection or Statement object can be retrieved from the respective proxy object via calling the proxy's `unwrap` method.
  * Providing [records](https://code.google.com/p/vibur-dbcp/source/browse/src/main/java/org/vibur/dbcp/jmx/ViburDBCPMonitoring.java#192) for all JDBC connections which are currently taken, including the stack traces with which they were taken. Useful if debugging lost (unclosed) connections.
  * JMX support - the pool registers an MBean via which various pool parameters can be observed and/or set.

#### Code Metrics and Performance Results ####

The below source code metrics are not accounting for the projects' testing directories
and for the Apache License header which is at the top of each source file:

| **Project** | **Source Files** | **Lines of Code** |
|:------------|:-----------------|:------------------|
| Vibur DBCP | 24 | 2514|
| Vibur Object Pool | 7 | 801 |

The below performance results were obtained via running this [test](https://code.google.com/p/vibur-dbcp/source/browse/src/test/java/org/vibur/dbcp/perf/ViburDBCPGetConnectionTestPerf.java#26) with Java 1.7 on a machine with an Intel i7-3615QM 2.3 GHz processor. The test was run with settings of 500 threads, each thread attempting 100 take/restore operations from a pool with initial size 50 and max size 200. Each thread simulated work for 5 or 10 milliseconds via calling Thread.sleep(), and the pool fairness parameter was set as shown in the table. The execution time was calculated as the average of three consecutive runs.

| **Pool Fairness** | **Simulated Work for** | **Execution Time** |
|:------------------|:-----------------------|:-------------------|
| true | 5 ms | 1753 ms |
| false | 5 ms | 1904 ms |
| true | 10 ms | 3035 ms |
| false | 10 ms | 3452 ms |


<br />
### Usage and Configuration ###

#### Maven coordinates and how to build: ####

```
<dependency>
  <groupId>org.vibur</groupId>
  <artifactId>vibur-dbcp</artifactId>
  <version>1.2.0</version>
</dependency>
```

To get a local copy of the Vibur DBCP repository use this command:

> `git clone https://code.google.com/p/vibur-dbcp/`

If needed, checkout a particular `tag` via something like:

> `git checkout tags/1.2.0`

Vibur DBCP uses an in-memory HyperSQL database for unit/integration testing purposes, and building the sources is simply a matter of executing:

> `mvn clean install`

#### Hibernate 3.x/4.x integration: ####

Vibur DBCP comes with [Hibernate3](https://code.google.com/p/vibur-dbcp-hibernate3/) and [Hibernate4](https://code.google.com/p/vibur-dbcp-hibernate4/) integrations. Depending on which Hibernate version your project uses, add one of the following Maven dependencies to it. Note that these dependencies will transitively include the shown above vibur-dbcp dependency, too.

<table border='0'>
<tr>
<td align='left' valign='top'>
<pre><code>&lt;!-- For Hibernate 4.3+ projects: --&gt;<br>
&lt;dependency&gt;<br>
  &lt;groupId&gt;org.vibur&lt;/groupId&gt;<br>
  &lt;artifactId&gt;vibur-dbcp-hibernate4&lt;/artifactId&gt;<br>
  &lt;version&gt;1.2.0&lt;/version&gt;<br>
&lt;/dependency&gt;<br>
</code></pre>
</td>

<td align='left' valign='top'>
<pre><code>&lt;!-- For Hibernate 4.0-4.2 projects: --&gt;<br>
&lt;dependency&gt;<br>
  &lt;groupId&gt;org.vibur&lt;/groupId&gt;<br>
  &lt;artifactId&gt;vibur-dbcp-hibernate4-012&lt;/artifactId&gt;<br>
  &lt;version&gt;1.2.0&lt;/version&gt;<br>
&lt;/dependency&gt;<br>
</code></pre>
</td>
</tr>
<tr>
<td align='left' valign='top'>
<pre><code>&lt;!-- For Hibernate 3.x projects: --&gt;<br>
&lt;dependency&gt;<br>
  &lt;groupId&gt;org.vibur&lt;/groupId&gt;<br>
  &lt;artifactId&gt;vibur-dbcp-hibernate3&lt;/artifactId&gt;<br>
  &lt;version&gt;1.2.0&lt;/version&gt;<br>
&lt;/dependency&gt;<br>
</code></pre>
</td><td align='left' valign='top'>
</td>
</tr>
</table>

#### Hibernate 3.x/4.x configuration snippet: ####

```
<hibernate-configuration>
    <session-factory>
        <!-- Database connection settings: -->
        <property name="hibernate.connection.url">jdbc:hsqldb:mem:sakila;shutdown=false</property>
        <property name="hibernate.connection.username">sa</property>
        <property name="hibernate.connection.password"></property>

        <property name="hibernate.dialect">org.hibernate.dialect.HSQLDialect</property>

        <property name="hibernate.current_session_context_class">thread</property>

        <!-- Vibur DBCP specific properties: -->
        <property name="hibernate.connection.provider_class">
            org.vibur.dbcp.integration.ViburDBCPConnectionProvider
        </property>

        <property name="hibernate.vibur.poolInitialSize">10</property>
        <property name="hibernate.vibur.poolMaxSize">100</property>

        <property name="hibernate.vibur.connectionIdleLimitInSeconds">30</property>
        <property name="hibernate.vibur.testConnectionQuery">isValid</property>

        <property name="hibernate.vibur.logQueryExecutionLongerThanMs">500</property>
        <property name="hibernate.vibur.logStackTraceForLongQueryExecution">true</property>

        <property name="hibernate.vibur.statementCacheMaxSize">200</property>
    </session-factory>
</hibernate-configuration>
```

#### Spring with Hibernate 3.x/4.x configuration snippet: ####

```
   <!-- Vibur DBCP dataSource bean definition: -->
   <bean id="dataSource" class="org.vibur.dbcp.ViburDBCPDataSource" init-method="start" destroy-method="terminate">
        <property name="jdbcUrl" value="jdbc:hsqldb:mem:sakila;shutdown=false"/>
        <property name="username" value="sa"/>
        <property name="password" value=""/>
		        
        <property name="poolInitialSize">10</property>
        <property name="poolMaxSize">100</property>

        <property name="connectionIdleLimitInSeconds">30</property>
        <property name="testConnectionQuery">isValid</property>

        <property name="logQueryExecutionLongerThanMs" value="500"/>
        <property name="logStackTraceForLongQueryExecution" value="true"/>
		
        <property name="statementCacheMaxSize" value="200"/>
    </bean>

    <!-- For Hibernate4 set the sessionFactory class below to org.springframework.orm.hibernate4.LocalSessionFactoryBean -->
    <bean id="sessionFactory" class="org.springframework.orm.hibernate3.LocalSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <property name="packagesToScan" value="the.project.packages"/>
        <property name="hibernateProperties">
            <props>
                <prop key="hibernate.dialect">org.hibernate.dialect.HSQLDialect</prop>
                <prop key="hibernate.cache.use_second_level_cache">false</prop>
                <prop key="hibernate.cache.use_query_cache">true</prop>
            </props>
        </property>
    </bean>

    <!-- For Hibernate4 set the transactionManager class below to org.springframework.orm.hibernate4.HibernateTransactionManager -->
    <bean id="transactionManager" class="org.springframework.orm.hibernate3.HibernateTransactionManager">
        <property name="sessionFactory" ref="sessionFactory"/>
    </bean>
```

#### Programming configuration snippet: ####

```
    public ViburDBCPDataSource createDataSourceWithStatementsCache() {
        ViburDBCPDataSource ds = new ViburDBCPDataSource();

        ds.setJdbcUrl("jdbc:hsqldb:mem:sakila;shutdown=false");
        ds.setUsername("sa");
        ds.setPassword("");

        ds.setPoolInitialSize(10);
        ds.setPoolMaxSize(100);

        ds.setConnectionIdleLimitInSeconds(30);
        ds.setTestConnectionQuery("isValid");

        ds.setLogQueryExecutionLongerThanMs(500);
        ds.setLogStackTraceForLongQueryExecution(true);

        ds.setStatementCacheMaxSize(200);

        ds.start();
        return ds;
    }
```

#### Log4j configuration snippet: ####

```
    <logger name="org.vibur.dbcp" additivity="false">
        <level value="debug"/>
        <appender-ref ref="console"/>
    </logger>
```

Here is a long running SQL queries [log sample](SQLQueriesLogSample.md) and a long lasting getConnection() calls [log sample](getConnectionLogSample.md).<br>


<br />
<h3>Configuration Options</h3>

<h4>Database connection settings:</h4>
<table><thead><th> <b>Attribute</b> </th><th> <b>Description</b> </th><th> <b>Default value</b> </th></thead><tbody>
<tr><td> <code>driverClassName</code> </td><td> Database driver class name. This is <b>an optional</b> parameter if the driver is JDBC 4 complaint. If specified, a call to <code>Class.forName(driverClassName).newInstance()</code> will be issued during the Vibur DBCP initialization. This is needed when Vibur DBCP is used in an OSGi container and may also be helpful if Vibur DBCP is used in an Apache Tomcat web application which has its JDBC driver JAR file packaged in the app WEB-INF/lib directory. If this property is not specified, Vibur DBCP will fallback to the standard JavaSE Service Provider mechanism in order to find the driver. </td><td> <code>null</code> </td></tr>
<tr><td> <code>jdbcUrl</code> </td><td> Database JDBC connection string. </td><td> Supplied by user </td></tr>
<tr><td> <code>username</code> </td><td> User name to use. </td><td> Supplied by user </td></tr>
<tr><td> <code>password</code> </td><td> Password to use. </td><td> Supplied by user </td></tr>
<tr><td> <code>externalDataSource</code> </td><td> If specified, this <code>externalDataSource</code> will be used as an alternative way to obtain the raw connections for the pool instead of calling <code>DriverManager.getConnection()</code>.  </td><td> <code>null</code> </td></tr></tbody></table>

<h4>Pool size, growth and fairness settings:</h4>
<table><thead><th> <b>Attribute</b> </th><th> <b>Description</b> </th><th> <b>Default value</b> </th></thead><tbody>
<tr><td> <code>poolInitialSize</code> </td><td> The pool initial size, i.e. the initial number of JDBC connections allocated in this pool. </td><td> <code>10</code> </td></tr>
<tr><td> <code>poolMaxSize</code> </td><td> The pool max size, i.e. the maximum number of JDBC connections allocated in this pool. </td><td> <code>100</code> </td></tr>
<tr><td> <code>poolFair</code> </td><td> If <code>true</code>, guarantees that the threads invoking the pool's <code>take</code> methods will be selected to obtain a connection from it in FIFO order, and no thread will be starved out from accessing the pool's underlying resources. </td><td> <code>true</code> </td></tr>
<tr><td> <code>poolEnableConnectionTracking</code> </td><td> If <code>true</code>, the pool will keep information for the current stack trace of every taken connection. </td><td> <code>false</code> </td></tr></tbody></table>

<h4>Database connection validation settings:</h4>
<table><thead><th> <b>Attribute</b> </th><th> <b>Description</b> </th><th> <b>Default value</b> </th></thead><tbody>
<tr><td> <code>connectionIdleLimitInSeconds</code> </td><td> If the connection has stayed in the pool for at least <code>connectionIdleLimitInSeconds</code>, it will be validated using the <code>testConnectionQuery</code> before being given to the application. If set to <code>0</code>, will validate the connection always when it is taken from the pool. If set to a negative number, will never validate the taken from the pool connection. </td><td> <code>15</code> </td></tr>
<tr><td> <code>testConnectionQuery</code> </td><td> Used to test the validity of a JDBC Connection. If the <code>connectionIdleLimitInSeconds</code> is set to a non-negative number, the <code>testConnectionQuery</code> should be set to a valid SQL query, for example <code>SELECT 1</code>, or to <code>isValid</code> in which case the <code>Connection.isValid()</code> method will be used. <p>Similarly to the spec for <a href='http://docs.oracle.com/javase/7/docs/api/java/sql/Connection.html#isValid%28int%29'>Connection.isValid(int)</a>, if a custom <code>testConnectionQuery</code> is specified, it will be executed in the context of the current transaction. </td><td> <code>isValid</code> </td></tr>
<tr><td> <code>initSQL</code> </td><td> An SQL query which will be run only once when a JDBC Connection is first created. This property should be set to a valid SQL query, to <code>null</code> which means no query, or to <code>isValid</code> which means that the <code>Connection.isValid()</code> method will be used. An use case in which this property can be useful is when the application is connecting to the database via some middleware, for example, connecting to PostgreSQL server(s) via PgBouncer. </td><td> <code>null</code> </td></tr></tbody></table>

<h4>Database connection timeout and retries settings:</h4>
<table><thead><th> <b>Attribute</b> </th><th> <b>Description</b> </th><th> <b>Default value</b> </th></thead><tbody>
<tr><td> <code>connectionTimeoutInMs</code> </td><td> Time to wait before a call to <code>getConnection()</code> times out and returns an error, for the case when there is an available and valid connection in the pool. <code>0</code> means forever. <p> If at the moment of the call there is not an available and valid connection and if the maximum number of allowed connections is not yet reached, the pool will try to create a new one (and retry if necessary), in which case the total maximum time which the call may take can be calculated as: <p> maxTimeoutInMs = connectionTimeoutInMs <br> + (acquireRetryAttempts + 1) <code>*</code> loginTimeoutInSeconds <code>*</code> 1000 <br> + acquireRetryAttempts <code>*</code> acquireRetryDelayInMs </td><td> <code>30000</code> </td></tr>
<tr><td> <code>loginTimeoutInSeconds</code> </td><td> Login timeout which will be passed to <code>DriverManager.setLoginTimeout()</code> or <code>getExternalDataSource().setLoginTimeout()</code> method during the initialization process of the DataSource. </td><td> <code>10</code> </td></tr>
<tr><td> <code>acquireRetryDelayInMs</code> </td><td> After attempting to acquire a JDBC connection and failing with an <code>SQLException</code>, wait for this long time before attempting to acquire a new JDBC connection again. </td><td> <code>1000</code> </td></tr>
<tr><td> <code>acquireRetryAttempt</code> </td><td> After attempting to acquire a JDBC connection and failing with an <code>SQLException</code>, retry to connect these many times before giving up. </td><td> <code>3</code> </td></tr></tbody></table>

<h4>JDBC statement caching settings:</h4>
<table><thead><th> <b>Attribute</b> </th><th> <b>Description</b> </th><th> <b>Default value</b> </th></thead><tbody>
<tr><td> <code>statementCacheMaxSize</code> </td><td> Defines the maximum statement cache size. <code>0</code> disables it, and the max allowed size is <code>1000</code>. If the statement's cache is not enabled, the client application may safely exclude the dependency on ConcurrentLinkedCacheMap. </td><td> <code>0</code> </td></tr></tbody></table>

<h4>Database connection and SQL queries logging settings:</h4>
<table><thead><th> <b>Attribute</b> </th><th> <b>Description</b> </th><th> <b>Default value</b> </th></thead><tbody>
<tr><td> <code>logConnectionLongerThanMs</code> </td><td> <code>getConnection</code> method calls taking longer than or equal to this time limit are logged at the WARN level. A value of <code>0</code> will log all such calls. A <code>negative number</code> disables it. </td><td> <code>3000</code> </td></tr>
<tr><td> <code>logStackTraceForLongConnection</code> </td><td> Will apply only if <code>logConnectionLongerThanMs</code> is enabled, and if set to <code>true</code>, will log at the WARN level the current <code>getConnection</code> call stack trace plus the time taken. </td><td> <code>false</code> </td></tr>
<tr><td> <code>logQueryExecutionLongerThanMs</code> </td><td> The underlying SQL queries from a JDBC Statement <code>execute...</code> calls taking longer than or equal to this time limit are logged at the WARN level. A value of <code>0</code> will log all such calls. A <code>negative number</code> disables it. <p>Note that while a JDBC Statement <code>execute...</code> call duration is roughly equivalent to the execution time of the underlying SQL query, the overall call duration may also include some Java GC time, JDBC driver specific execution time, and context switching time (the last could be significant if the application has a very large thread count). </td><td> <code>3000</code> </td></tr>
<tr><td> <code>logStackTraceForLongQueryExecution</code> </td><td> Will apply only if <code>logQueryExecutionLongerThanMs</code> is enabled, and if set to <code>true</code>, will log at the WARN level the current JDBC Statement <code>execute...</code> call stack trace plus the underlying SQL query and the time taken. </td><td> <code>false</code> </td></tr></tbody></table>

<h4>Database connection default behavior settings:</h4>
<table><thead><th> <b>Attribute</b> </th><th> <b>Description</b> </th><th> <b>Default value</b> </th></thead><tbody>
<tr><td> <code>resetDefaultsAfterUse</code> </td><td> If set to <code>true</code>, will reset the connection default values below, always after the connection is restored (returned) to the pool after use. If the calling application never changes these default values, resetting them is not needed. </td><td> <code>false</code> </td></tr>
<tr><td> <code>defaultAutoCommit</code> </td><td> The default auto-commit state of the created connections. </td><td> Driver's default </td></tr>
<tr><td> <code>defaultReadOnly</code> </td><td> The default read-only state of the created connections. </td><td> Driver's default </td></tr>
<tr><td> <code>defaultTransactionIsolation</code> </td><td> The default transaction isolation level (as string value) for the created connections. </td><td> Driver's default </td></tr>
<tr><td> <code>defaultCatalog</code> </td><td> The default catalog of the created connections. </td><td> Driver's default </td></tr></tbody></table>

<h4>JMX related settings:</h4>
<table><thead><th> <b>Attribute</b> </th><th> <b>Description</b> </th><th> <b>Default value</b> </th></thead><tbody>
<tr><td> <code>enableJMX</code> </td><td> Enables or disables the DataSource JMX exposure. </td><td> <code>true</code> </td></tr>
<tr><td> <code>name</code> </td><td> The DataSource name, mostly useful for JMX identification and similar. This <code>name</code> must be unique among all names for all configured DataSources. The default name is an auto generated integer id. If the configured <code>name</code> is not unique, the default auto generated id will be used instead. </td><td> Auto generated id </td></tr></tbody></table>

<h4>Pool size reduction settings (advanced):</h4>
<table><thead><th> <b>Attribute</b> </th><th> <b>Description</b> </th><th> <b>Default value</b> </th></thead><tbody>
<tr><td> <code>reducerTimeIntervalInSeconds</code> </td><td> The time period after which the <code>poolReducer</code> will try to (possibly) reduce the number of created but unused JDBC connections in this pool. <code>0</code> disables it. </td><td> <code>60</code> </td></tr>
<tr><td> <code>reducerSamples</code> </td><td> How many times the <code>poolReducer</code> will wake up during the given <code>reducerTimeIntervalInSeconds</code> period in order to sample various statistics from the pool. </td><td> <code>20</code> </td></tr></tbody></table>


<br />
<h3>Development Team</h3>

Vibur DBCP is designed and developed by Simeon Malchev (firstname.lastname at gmail.com).<br>
If you need to report any issues, to request features, or if you just wish to provide some general feedback, please use the<br>
project's issues tracking <a href='https://code.google.com/p/vibur-dbcp/issues/list'>system</a> or the author's email.<br>
<br>
<br>
<h4>Acknowledgements</h4>

I would like to thank to all my friends and colleagues who reviewed the original project documentation and source code.<br>
Your encouragement and feedback helped me to bring the project to its current state.<br>
<br>
Vibur DBCP was developed from scratch, however while working on it the author has often looked at the documentation<br>
and/or source code of projects such as Spring Framework, BoneCP, Tomcat JDBC Connection Pool, HikariCP, between others.