# 配置说明

本文介绍使用Nebula Exchange时如何修改配置文件[`application.conf`](https://github.com/vesoft-inc/nebula-exchange/blob/master/nebula-exchange/src/main/resources/application.conf)。

修改配置文件之前，建议根据数据源复制并修改文件名称，便于区分。例如数据源为CSV文件，可以复制为`csv_application.conf`。

配置文件的内容主要分为如下几类：

- Spark相关配置

- Hive配置（可选）

- Nebula Graph相关配置

- 点配置

- 边配置

## Spark相关配置

本文只列出部分Spark参数，更多参数请参见[官方文档](https://spark.apache.org/docs/latest/configuration.html#application-properties)。

|参数|数据类型|默认值|是否必须|说明|
|:---|:---|:---|:---|:---|
|`spark.app.name`|string|-|否|Spark驱动程序名称。|
|`spark.driver.cores`|int|`1`|否|驱动程序使用的CPU核数，仅适用于集群模式。|
|`spark.driver.maxResultSize`|string|`1G`|否|单个Spark操作（例如collect）时，所有分区的序列化结果的总大小限制（字节为单位）。最小值为1M，0表示无限制。|
|`spark.executor.memory`|string|`1G`|否|Spark驱动程序使用的内存量，可以指定单位，例如512M、1G。|
|`spark.cores.max`|int|`16`|否|当驱动程序以“粗粒度”共享模式在独立部署集群或Mesos集群上运行时，跨集群（而非从每台计算机）请求应用程序的最大CPU核数。如果未设置，则值为Spark的独立集群管理器上的`spark.deploy.defaultCores`或Mesos上的infinite（所有可用的内核）。|

## Hive配置（可选）

如果Spark和Hive部署在不同集群，才需要配置连接Hive的参数，否则请忽略这些配置。

|参数|数据类型|默认值|是否必须|说明|
|:---|:---|:---|:---|:---|
|`hive.warehouse`|string|-|是|HDFS中的warehouse路径。用双引号括起路径，以`hdfs://`开头。|
|`hive.connectionURL`|string|-|是|JDBC连接的URL。例如`"jdbc:mysql://127.0.0.1:3306/hive_spark?characterEncoding=UTF-8"`。|
|`hive.connectionDriverName`|string|`"com.mysql.jdbc.Driver"`|是|驱动名称。|
|`hive.connectionUserName`|list\[string\]|-|是|连接的用户名。|
|`hive.connectionPassword`|list\[string\]|-|是|用户名对应的密码。|

## Nebula Graph相关配置

|参数|数据类型|默认值|是否必须|说明|
|:---|:---|:---|:---|:---|
|`nebula.address.graph`|list\[string\]|`["127.0.0.1:9669"]`|是|所有Graph服务的地址，包括IP和端口，多个地址用英文逗号（,）分隔。格式为`["ip1:port1","ip2:port2","ip3:port3"]`。|
|`nebula.address.meta`|list\[string\]|`["127.0.0.1:9559"]`|是|所有Meta服务的地址，包括IP和端口，多个地址用英文逗号（,）分隔。格式为`["ip1:port1","ip2:port2","ip3:port3"]`。|
|`nebula.user`|string|-|是|拥有Nebula Graph写权限的用户名。|
|`nebula.pswd`|string|-|是|用户名对应的密码。|
|`nebula.space`|string|-|是|需要导入数据的的图空间名称。|
|`nebula.ssl.enable.graph`|bool|`false`|是|开启Exchange与Graph服务之间的[SSL加密](https://en.wikipedia.org/wiki/Transport_Layer_Security)传输。当值为`true`时开启，下方的SSL相关参数生效。如果Exchange运行在多机集群上，在设置以下SSL相关路径时，需要在每台机器的相同路径都存储相应的文件。|
|`nebula.ssl.sign`|string|`ca`|是|签名方式，可选值：`ca`（CA签名）或`self`（自签名）。|
|`nebula.ssl.ca.param.caCrtFilePath`|string|`"/path/caCrtFilePath"`|是|`nebula.ssl.sign`的值为`ca`时生效，用于指定CA证书的存储路径。|
|`nebula.ssl.ca.param.crtFilePath`|string|`"/path/crtFilePath"`|是|`nebula.ssl.sign`的值为`ca`时生效，用于指定CRT证书的存储路径。|
|`nebula.ssl.ca.param.keyFilePath`|string|`"/path/keyFilePath"`|是|`nebula.ssl.sign`的值为`ca`时生效，用于指定私钥文件的存储路径。|
|`nebula.ssl.self.param.crtFilePath`|string|`"/path/crtFilePath"`|是|`nebula.ssl.sign`的值为`self`时生效，用于指定CRT证书的存储路径。|
|`nebula.ssl.self.param.keyFilePath`|string|`"/path/keyFilePath"`|是|`nebula.ssl.sign`的值为`self`时生效，用于指定私钥文件的存储路径。|
|`nebula.ssl.self.param.password`|string|`"nebula"`|是|`nebula.ssl.sign`的值为`self`时生效，用于指定密码文件的存储路径。|
|`nebula.path.local`|string|`"/tmp"`|否|导入SST文件时需要设置本地SST文件路径。|
|`nebula.path.remote`|string|`"/sst"`|否|导入SST文件时需要设置远端SST文件路径。|
|`nebula.path.hdfs.namenode`|string|`"hdfs://name_node:9000"`|否|导入SST文件时需要设置HDFS的namenode。|
|`nebula.connection.timeout`|int|`3000`|否|Thrift连接的超时时间，单位为 ms。|
|`nebula.connection.retry`|int|`3`|否|Thrift连接重试次数。|
|`nebula.execution.retry`|int|`3`|否|nGQL语句执行重试次数。|
|`nebula.error.max`|int|`32`|否|导入过程中的最大失败次数。当失败次数达到最大值时，提交的Spark作业将自动停止。|
|`nebula.error.output`|string|`/tmp/errors`|否|输出错误日志的路径。错误日志保存执行失败的nGQL语句。|
|`nebula.rate.limit`|int|`1024`|否|导入数据时令牌桶的令牌数量限制。|
|`nebula.rate.timeout`|int|`1000`|否|令牌桶中拿取令牌的超时时间，单位：毫秒。|

## 点配置

对于不同的数据源，点的配置也有所不同，有很多通用参数，也有部分特有参数，配置时需要配置通用参数和不同数据源的特有参数。

### 通用参数

|参数|数据类型|默认值|是否必须|说明|
|:---|:---|:---|:---|:---|
|`tags.name`|string|-|是|Nebula Graph中定义的Tag名称。|
|`tags.type.source`|string|-|是|指定数据源。例如`csv`。|
|`tags.type.sink`|string|`client`|是|指定导入方式，可选值为`client`和`SST`。|
|`tags.fields`|list\[string\]|-|是|属性对应的列的表头或列名。如果有表头或列名，请直接使用该名称。如果CSV文件没有表头，用`[_c0, _c1, _c2]`的形式表示第一列、第二列、第三列，以此类推。|
|`tags.nebula.fields`|list\[string\]|-|是|Nebula Graph中定义的属性名称，顺序必须和`tags.fields`一一对应。例如`[_c1, _c2]`对应`[name, age]`，表示第二列为属性name的值，第三列为属性age的值。|
|`tags.vertex.field`|string|-|是|点ID的列。例如CSV文件没有表头时，可以用`_c0`表示第一列的值作为点ID。|
|`tags.batch`|int|`256`|是|单批次写入Nebula Graph的最大点数量。|
|`tags.partition`|int|`32`|是|Spark分片数量。|

### Parquet/JSON/ORC源特有参数

|参数|数据类型|默认值|是否必须|说明|
|:---|:---|:---|:---|:---|
|`tags.path`|string|-|是|HDFS中点数据文件的路径。用双引号括起路径，以`hdfs://`开头。|

### CSV源特有参数

|参数|数据类型|默认值|是否必须|说明|
|:---|:---|:---|:---|:---|
|`tags.path`|string|-|是|HDFS中点数据文件的路径。用双引号括起路径，以`hdfs://`开头。|
|`tags.separator`|string|`,`|是|分隔符。默认值为英文逗号（,）。|
|`tags.header`|bool|`true`|是|文件是否有表头。|

### Hive源特有参数

|参数|数据类型|默认值|是否必须|说明|
|:---|:---|:---|:---|:---|
|`tags.exec`|string|-|是|查询数据源的语句。例如`select name,age from mooc.users`。|

### MaxCompute源特有参数

|参数|数据类型|默认值|是否必须|说明|
|:---|:---|:---|:---|:---|
|`tags.table`|string|-|是|MaxCompute的表名。|
|`tags.project`|string|-|是|MaxCompute的项目名。|
|`tags.odpsUrl`|string|-|是|MaxCompute服务的odpsUrl。地址可根据[阿里云文档](https://help.aliyun.com/document_detail/34951.html)查看。|
|`tags.tunnelUrl`|string|-|是|MaxCompute服务的tunnelUrl。地址可根据[阿里云文档](https://help.aliyun.com/document_detail/34951.html)查看。|
|`tags.accessKeyId`|string|-|是|MaxCompute服务的accessKeyId。|
|`tags.accessKeySecret`|string|-|是|MaxCompute服务的accessKeySecret。|
|`tags.partitionSpec`|string|-|否|MaxCompute表的分区描述。|
|`tags.sentence`|string|-|否|查询数据源的语句。SQL语句中的表名和上方table的值相同。|

### Neo4j源特有参数

|参数|数据类型|默认值|是否必须|说明|
|:---|:---|:---|:---|:---|
|`tags.exec`|string|-|是|查询数据源的语句。例如`match (n:label) return n.neo4j-field-0`。|
|`tags.server`|string|`"bolt://127.0.0.1:7687"`|是|Neo4j服务器地址。|
|`tags.user`|string|-|是|拥有读取权限的Neo4j用户名。|
|`tags.password`|string|-|是|用户名对应密码。|
|`tags.database`|string|-|是|Neo4j中保存源数据的数据库名。|
|`tags.check_point_path`|string|`/tmp/test`|否|设置保存导入进度信息的目录，用于断点续传。如果未设置，表示不启用断点续传。|

### MySQL源特有参数

|参数|数据类型|默认值|是否必须|说明|
|:---|:---|:---|:---|:---|
|`tags.host`|string|-|是|MySQL服务器地址。|
|`tags.port`|string|-|是|MySQL服务器端口。|
|`tags.database`|string|-|是|数据库名称。|
|`tags.table`|string|-|是|需要作为数据源的表名称。|
|`tags.user`|string|-|是|拥有读取权限的MySQL用户名。|
|`tags.password`|string|-|是|用户名对应密码。|
|`tags.sentence`|string|-|是|查询数据源的语句。例如`"select teamid, name from basketball.team order by teamid;"`。|

### ClickHouse源特有参数

|参数|数据类型|默认值|是否必须|说明|
|:---|:---|:---|:---|:---|
|`tags.url`|string|-|是|ClickHouse的JDBC URL。|
|`tags.user`|string|-|是|有读取权限的ClickHouse用户名。|
|`tags.password`|string|-|是|用户名对应密码。|
|`tags.numPartition`|string|-|是|ClickHouse分区数。|
|`tags.sentence`|string|-|是|查询数据源的语句。|

### Hbase源特有参数

|参数|数据类型|默认值|是否必须|说明|
|:---|:---|:---|:---|:---|
|`tags.host`|string|`127.0.0.1`|是|Hbase服务器地址。|
|`tags.port`|string|`2181`|是|Hbase服务器端口。|
|`tags.table`|string|-|是|需要作为数据源的表名称。|
|`tags.columnFamily`|string|-|是|表所属的列族（column family）。|

### Pulsar源特有参数

|参数|数据类型|默认值|是否必须|说明|
|:---|:---|:---|:---|:---|
|`tags.service`|string|`"pulsar://localhost:6650"`|是|Pulsar服务器地址。|
|`tags.admin`|string|`"http://localhost:8081"`|是|连接pulsar的admin.url。|
|`tags.options.<topic\|topics\| topicsPattern>`|string|-|是|Pulsar的选项，可以从`topic`、`topics`和`topicsPattern`选择一个进行配置。|
|`tags.interval.seconds`|int|`10`|是|读取消息的间隔。单位：秒。|

### Kafka源特有参数

|参数|数据类型|默认值|是否必须|说明|
|:---|:---|:---|:---|:---|
|`tags.service`|string|-|是|Kafka服务器地址。|
|`tags.topic`|string|-|是|消息类别。|
|`tags.interval.seconds`|int|`10`|是|读取消息的间隔。单位：秒。|

### SST源特有参数

|参数|数据类型|默认值|是否必须|说明|
|:---|:---|:---|:---|:---|
|`tags.path`|string|-|是|指定需要生成SST文件的源文件的路径。|

### Nebula Graph源特有参数

!!! enterpriseonly

    Nebula Graph源特有参数用于导出Nebula Graph数据，仅企业版Exchange支持。

|参数|数据类型|默认值|是否必须|说明|
|:---|:---|:---|:---|:---|
|`tags.path`|string|`"hdfs://namenode:9000/path/vertex"`|是|指定CSV文件的存储路径。设置的路径必须不存在，Exchange会自动创建该路径。存储到HDFS服务器时路径格式同默认值，例如`"hdfs://192.168.8.177:9000/vertex/player"`。存储到本地时路径格式为`"file:///path/vertex"`，例如`"file:///home/nebula/vertex/player"`。有多个Tag时必须为每个Tag设置不同的目录。|
|`tags.noField`|bool|`false`|是|当值为`true`时，仅导出VID而不导出属性数据；当值为`false`时导出VID和属性数据。|
|`tags.return.fields`|list|`[]`|是|指定要导出的属性。例如，要导出`name`和`age`属性，需将参数值设置为`["name","age"]`。该参数仅在`tags.noField`的值为`false`时生效。|

## 边配置

对于不同的数据源，边的配置也有所不同，有很多通用参数，也有部分特有参数，配置时需要配置通用参数和不同数据源的特有参数。

边配置的不同数据源特有参数请参见上方点配置内的特有参数介绍，注意区分tags和edges即可。

### 通用参数

|参数|数据类型|默认值|是否必须|说明|
|:---|:---|:---|:---|:---|
|`edges.name`| string|-|是|Nebula Graph中定义的Edge type名称。|
|`edges.type.source`|string|-|是|指定数据源。例如`csv`。|
|`edges.type.sink`|string|`client`|是|指定导入方式，可选值为`client`和`SST`。|
|`edges.fields`|list\[string\]|-|是|属性对应的列的表头或列名。如果有表头或列名，请直接使用该名称。如果CSV文件没有表头，用`[_c0, _c1, _c2]`的形式表示第一列、第二列、第三列，以此类推。|
|`edges.nebula.fields`|list\[string\]|-|是|Nebula Graph中定义的属性名称，顺序必须和`edges.fields`一一对应。例如`[_c2, _c3]`对应`[start_year, end_year]`，表示第三列为开始年份的值，第四列为结束年份的值。|
|`edges.source.field`|string|-|是|边的起始点的列。例如`_c0`表示第一列的值作为边的起始点。|
|`edges.target.field`|string|-|是|边的目的点的列。例如`_c1`表示第二列的值作为边的目的点。|
|`edges.ranking`|int|-|否|rank值的列。没有指定时，默认所有rank值为`0`。|
|`edges.batch`|int|`256`|是|单批次写入Nebula Graph的最大边数量。|
|`edges.partition`|int|`32`|是|Spark分片数量。|

### Nebula Graph源特有参数

|参数|数据类型|默认值|是否必须|说明|
|:---|:---|:---|:---|:---|
|`edges.path`|string|`"hdfs://namenode:9000/path/edge"`|是|指定CSV文件的存储路径。设置的路径必须不存在，Exchange会自动创建该路径。存储到HDFS服务器时路径格式同默认值，例如`"hdfs://192.168.8.177:9000/edge/follow"`。存储到本地时路径格式为`"file:///path/edge"`，例如`"file:///home/nebula/edge/follow"`。有多个Edge时必须为每个Edge设置不同的目录。|
|`edges.noField`|bool|`false`|是|当值为`true`时，仅导出起始点VID、目的点VID和Rank，而不导出属性数据；当值为`false`时导出起始点VID、目的点VID、Rank和属性数据。|
|`edges.return.fields`|list|`[]`|是|指定要导出的属性。例如，要导出`start_year`和`end_year`属性，需将参数值设置为`["start_year","end_year"]`。该参数仅在`edges.noField`的值为`false`时生效。|