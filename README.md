# Elasticsearch 参考

搜索引擎 Search Engine，简称 ES，类似搜索引擎 Apache Solr，根据 Google Trends 目前热度貌似不如 ES  
都是基于 Apache Lucene™ 的开源搜索引擎  
无论在开源还是专有领域，Lucene 可以被认为是迄今为止最先进、性能最好的、功能最全的搜索引擎库。 

文档 https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html  
源码 https://github.com/elastic/elasticsearch

Elastic 公司，先 Elasticsearch 成功，再创立 Elastic 公司。  
附加产品 Logstash Kibana Beats，一同组成 Elastic Stack。

https://www.elastic.co/cn/what-is/open-x-pack  
X-Pack 简介 - X-Pack 为 Elastic Stack 带来了一系列深度集成的企业级功能，其中包括安全、告警、监测、报告、图表分析、专用 APM UI 和 Machine Learning。  
一些特殊功能包/功能代码的总称X-Pack  

Elastichsearch Service  
用户既可通过 Elasticsearch Service（在 Amazon Web Services (AWS)、Google Cloud 和阿里云上均有提供）以托管型服务的形式部署 Elasticsearch，也可自行下载并在自己的硬件上或在云端进行安装。

## 倒排索引 inverted index

主流搜索引擎都会使用倒排索引，ES也不例外，当然ES还会根据不同字段数据类型使用不同索引算法

如果对大量的文档进行搜索某个关键字，正常的逻辑是，遍历每个文档，找到有这个关键字的所有文档，这需要全量搜索，非常慢。  
倒排索引，先记录每个关键词出现在那些文档里，那么直接根据关键字就能拿到所有匹配的文档，这样效率快多了。  
这种方式，应该也是典型的，牺牲写的性能，来提高读的性能。

需要结合分词器来实现倒排索引

```text
假设 三篇文档
    id content
    0  谷歌地图之父跳槽Facebook
    1  谷歌地图之父加盟Facebook
    2  谷歌地图之父拉斯加加盟社交网站Facebook
如果不是倒排索引，可能的情况
    0 谷歌地图 之父 跳槽 Facebook
    1 谷歌地图 之父 加盟 Facebook
    2 谷歌地图 之父 拉斯加 加盟 社交网站 Facebook
如果是倒排索引，可能的情况
    单词ID 单词       倒排列表(文档ID)
    0     谷歌地图     0 1 2
    1     之父        0 1 2 
    2     跳槽        0
    3     Facebook   0 1 2
    4     加盟        1 2
    5     拉斯加      2
    6     社交网站    2
```

## index的增删改查

https://www.elastic.co/guide/en/elasticsearch/reference/current/indices.html

## 动态mapping和明确mapping 

https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping.html  
https://www.elastic.co/guide/en/elasticsearch/reference/current/explicit-mapping.html

## cluster and nodes

集群，一个集群里面可以有多个节点

## documents and indices 文档和索引

https://www.elastic.co/guide/en/elasticsearch/reference/current/documents-indices.html

1. ES是一个分布式的文档存储。不是存储存储有多个列的行数据，而是通过序列化为JSON documents来村粗复杂的数据结构。
2. 文档分布在集群的任意一个节点，可以从任意一个节点中获取；
3. 当文档被存储后，它能被索引和搜索几乎接近实时，1秒内
4. ES使用inverted index的数据结构，倒排索引，它能支持非常快的full-text searches
5. 一个inverted index lists每个出现在文档中的unique word
6. 一个index可以被认为是优化过的文档集合，每个文档是多个字段fields的集合，字段是键值对key-value pairs，包含了数据
7. 文本字段存储在倒排索引，数字和地理字段存储在BKD树
8. 默认开启动态映射，When dynamic mapping is enabled, Elasticsearch automatically detects and adds new fields to the index. This default behavior makes it easy to index and explore your data—​just start indexing documents and Elasticsearch will detect and map booleans, floating point and integer values, dates, and strings to the appropriate Elasticsearch data types.
9. ...

## 字段数据类型 Field Data Types

https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-types.html#object-types

1. 每个字段都有一个字段数据类型，或者叫字段类型
2. text字段被解析来做全文搜索，keyword字段被用来做过滤和排序
3. 字段类型通过family进行分组，在相同family的类型有完全一样的搜索欣慰，但是有不同的空间使用或性能
4. 当前有两个类型families, keyword 和 text。 其他类型families只有单一的字段类型。例如boolean type family包含一个字段类型 boolean
5. 常用类型Common Types。binary,boolean,Keywords,Numbers,Dates,alias
6. 对象和关系类型Objects and relational types。object,flattened,nested,join
7. 结构化数据类型Structured data types。Range, ip, version, murmur3
8. 聚合数据类型 Aggregate data types。aggregate_metric_double, histogram
9. 文本搜索类型 Text search types。text,annotated-text,completion(自动补全),search_as_you_type,token_count
10. 文档排序类型 Document ranking types。dense_vector,rank_feature,rank_features
11. 空间数据类型 Spatial data types。 geo_point(经纬度), geo_shape, point, shape
12. 其他类型Other types。percolator
13. 数组 Arrays，在Elasticsearch，数组不需要一个专门的字段数据类型。任何字段默认能包含零个或多个值，但是，在数组里面的所有值必须是同样的字段类型
14. 多字段 Multi-fields。It is often useful to index the same field in different ways for different purposes。例如可以将一个string字段映射为text字段(be mapped as a text field)为了全文搜索，并且映射为keyword为了排序和聚合。另外，可以用standard analyzer, english annalyzer和frech analyzer，来index一个text字段。大多数字段类型通过fields参数来支持多字段。

https://www.elastic.co/guide/en/elasticsearch/reference/current/keyword.html

字符串，根据不同的目的，可以设计为text或keyword字段类型，或者同时设计两个字段类型。
Avoid using keyword fields for full-text search. Use the text field type instead.

## 安装 并 测试 ik 分词器 (存储和搜索的分词器可以分开指定)

https://github.com/medcl/elasticsearch-analysis-ik

ik_max_word 和 ik_smart 什么区别?  
    ik_max_word: 会将文本做最细粒度的拆分，比如会将“中华人民共和国国歌”拆分为“中华人民共和国,中华人民,中华,华人,人民共和国,人民,人,民,共和国,共和,和,国国,国歌”，会穷尽各种可能的组合，适合 Term Query；  
    ik_smart: 会做最粗粒度的拆分，比如会将“中华人民共和国国歌”拆分为“中华人民共和国,国歌”，适合 Phrase 查询。  

```text
Install
1.download or compile
    optional 1 - download pre-build package from here: https://github.com/medcl/elasticsearch-analysis-ik/releases
        create plugin folder cd your-es-root/plugins/ && mkdir ik    
        unzip plugin to folder your-es-root/plugins/ik

    optional 2 - use elasticsearch-plugin to install ( supported from version v5.5.1 ):
        ./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v6.3.0/elasticsearch-analysis-ik-6.3.0.zip
        NOTE: replace 6.3.0 to your own elasticsearch version
2.restart elasticsearch
```

安装，重启ES

```text
https://github.com/medcl/elasticsearch-analysis-ik/releases
找到对应版本ES，版本不一致会报错
$ ./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v8.7.0/elasticsearch-analysis-ik-8.7.0.zip
$ pkill -F pid
$ ./bin/elasticsearch -d -p pid -Expack.security.enabled=false
```

测试ik分词器

```text
DELETE /news
PUT /news
{
  "mappings": {
    "properties": {
      "content": {
        "type": "text",
        "analyzer": "ik_max_word",
        "search_analyzer": "ik_smart"
      }
    }
  }
}
POST /news/_doc/
{"content":"这是一条新闻内容"}
POST /_analyze
{
  "text": "中国上海市", 
  "analyzer": "standard"
} -> 中 国 上 海 市
POST /_analyze
{
  "text": "中国上海市", 
  "analyzer": "ik_smart"
} -> 中国 上海市
POST /_analyze
{
  "text": "中国上海市", 
  "analyzer": "ik_max_word"
} -> 中国 上海市 上海 海市
```

## term精准搜索、match 分词匹配搜索、wildcard 通配符搜索、fuzzy 模糊纠错搜索

mysql like % 0个或多个任意字符 _ 1个任意字符

1. 删除users索引，如果已存在 
    GET /users GET /users/_mapping DELETE /users
2. 创建users索引
    ```text
    PUT /users
    {
      "mappings": {
        "properties": {
          "uid":    { "type": "long" },
          "name":   { 
            "type": "text", 
            "fields": { "keyword": {"type": "keyword"} }
          },    
          "age":    { "type": "integer" },  
          "email":  { "type": "keyword"  }
        }
      }
    }
    ```
3. 批量创建文档
    ```text
    POST /users/_bulk
    {"index":{"_id":"1"}}
    {"uid":1,"name":"张三","age":18,"email":"name@company.com"}
    {"index":{"_id":"2"}}
    {"uid":2,"name":"张三丰","age":18,"email":"name@company.com"}
    {"index":{"_id":"3"}}
    {"uid":3,"name":"张飞","age":18,"email":"name@company.com"}
    {"index":{"_id":"4"}}
    {"uid":4,"name":"三德子","age":18,"email":"name@company.com"}
    {"index":{"_id":"5"}}
    {"uid":5,"name":"张二丰","age":18,"email":"name@company.com"}
    {"index":{"_id":"6"}}
    {"uid":6,"name":"孙权","age":18,"email":"name@company.com"}
    {"index":{"_id":"7"}}
    {"uid":7,"name":"马三丰","age":18,"email":"name@company.com"}
    ```
4. 分词测试 (建议装一下ik等中文分词器，ik_smart, ik_max_word, 标准和内置的分词器适合英文语言，不适合中文，中文会被分为一个个汉字)
    ```text
    POST _analyze
    {
        "text": "张三"
    }
    结果 张 三 (没有张三整体，建议使用ik_smart或ik_max_word)
    ```
5. 精确搜索、分词搜索、通配符搜索、模糊搜索(类型为text，存储时会被变成分词存储，而不是整体)
    ```text
    # 精确搜索
    GET users/_search
    {"query":{"term":{"name.keyword":{"value":"张三"}}}}
    # 结果 张三
    
    # 分词搜索
    GET users/_search
    {"query": {"match": {"name": "张三"}}}
    # 结果 张三 张三丰 张飞 三德子 张二丰 马三丰
    
    # 通配符搜索
    GET users/_search
    {"query":{"wildcard":{"name.keyword":{"value":"张三*"}}}}
    # 结果 张三 张三丰
    
    # 模糊搜索 高CPU开销和低精确度 (允许一定字符的修改、添加、删除、修改位置)
    # 例如 1. box → fox  2. black → lack  3. sic → sick  4. act → cat
    # https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-fuzzy-query.html
    GET users/_search
    {"query":{"fuzzy":{"name.keyword":{"value":"张三丰"}}}}
    # 结果 张三丰 张二丰 马三丰 张三
    ```

## Search APIs 搜索 API

https://www.elastic.co/guide/en/elasticsearch/reference/8.8/search.html#search

```text
GET /<target>/_search
GET /_search
POST /<target>/_search
POST /_search
```

## Document 文档相关 API

https://www.elastic.co/guide/en/elasticsearch/reference/8.8/docs.html

Document APIs

**Index API**

如果索引里的文档存在，请求会更新文档，并增加它的version，+1

```text
PUT /<target>/_doc/<_id>
POST /<target>/_doc/
PUT /<target>/_create/<_id>
POST /<target>/_create/<_id>
    <target>
        (Required, string) Name of the data stream or index to target.
    <_id>
        (Optional, string) Unique identifier for the document.
        To automatically generate a document ID, use the POST /<target>/_doc/ request format and omit this parameter.
    Description: You can index a new JSON document with the _doc or _create resource. Using _create guarantees that the document is only indexed if it does not already exist. To update an existing document, you must use the _doc resource.

POST users_00/_doc/10
{
    "user_id": 10,
    "user_name": "张三",
    "age": 20
}
POST users_01/_doc/11
{
    "user_id": 11,
    "user_name": "张三丰",
    "age": 20
}
POST users_02/_doc/12
{
    "user_id": 12,
    "user_name": "张二",
    "age": 20
}
GET users_*/_search
```

## Kibana 安装、启动

https://www.elastic.co/guide/en/kibana/current/index.html  
https://www.elastic.co/guide/en/kibana/current/targz.html

ES 虽然强大，但操作不方便，可以需要借助 Kibana  
https://github.com/elastic/kibana  
开发语言 TypeScript 不是 Java

kibana 如何发音 how to pronounce kibana?

```
1. 下载 Kibana tar.gz 包
    https://www.elastic.co/cn/downloads/kibana
2. 修改配置，让所有主机都能访问
     $KIBANA_HOME/config/kibana.yml
     #server.host: "localhost"
    server.host: "0.0.0.0"    
3. 解压运行    
    不能root运行，
    $ chown -R es:es kibana-8.8.0
    $ su es
    $ bin/kibana 前台运行 (推荐运行方式 Ctrl-C 停止)
    $ [nohup] bin/kibana & 后台运行 (kill时使用netstat -anp | grep 5601找pid，而不是ps -ef | grep kibana)
4. 测试
    http://192.168.1.205:5601/
    Configuration recommended
    In a production environment, it is recommended that you configure server.publicBaseUrl. Learn more.
    选择 Explore on my own
    默认不需要配置 192.168.1.205:9200 的地址，直接绑定了该 ES
```

## Linux 安装、启动、停止 ES 

https://www.elastic.co/guide/en/elasticsearch/reference/current/targz.html#targz

压缩包 安装

```text
1. 下载 ES 并解压 ($ES_HOME/jdk，有绑定版本的JDK，不需要额外搭建JDK环境)
    https://www.elastic.co/downloads/elasticsearch 
    或者
    wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.8.1-linux-x86_64.tar.gz
    wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.8.1-linux-x86_64.tar.gz.sha512
    shasum -a 512 -c elasticsearch-8.8.1-linux-x86_64.tar.gz.sha512 
    tar -xzf elasticsearch-8.8.1-linux-x86_64.tar.gz
    cd elasticsearch-8.8.1/  (这个是$ES_HOME)
2. 默认 ES 允许自动索引创建，默认 action.auto_create_index: * 所有索引都能自动创建。
    如果关闭了自动索引创建，要使用某些商业功能，必须配置某些索引能自动创建 action.auto_create_index。
    当然如果没手动关闭自动索引创建，或者不需要商业功能，不需要这一步
    如果用Logstash或者Beats，某些索引也得配置为能自动创建。
3. 运行 ES
    $ bin/elasticsearch 前台运行，root运行会报错can not run elasticsearch as root
    $ groupadd es 
    $ useradd -g es es 
    $ id es
    uid=1002(es) gid=1002(es) groups=1002(es)
    $ chown -R es:es .
    $ su es
    $ bin/elasticsearch
    第一次运行，安全功能会被启动，安全配置会自动出现，
    1. 提供给 elastic 内置超级用户的密码会被生成
    2. HTTPS 密钥和证书会被生成
    3. 登录 Kibana 的 口令 Token 会被生成，30分钟内有效
    注：以后运行，不会在控制台中输出这些内容
    重置密码 bin/elasticsearch-reset-password -u elastic
    生成Kibana口令 bin/elasticsearch-create-enrollment-token -s kibana
4. 日志 默认 输出日志到 控制台 和 logs/<clustername>.log    
    启动时记录一些信息，初始化完后，不会记录更多信息，除非一些值得记录的事情发生了。
5. 加入其他节点 (暂时忽略)
6. 测试 默认端口 9200，默认需要https， http无法正常访问
    ① (未测试)curl --cacert $ES_HOME/config/certs/http_ca.crt -u elastic https://localhost:9200 
    证书都在config/certs里面
    ② 浏览器 访问 https://192.168.1.205:9200/ 输出账号密码
5. 停止 ES，Ctrl-C
```

优化

```text
1. 为了方便开发，让所有主机都能访问
    默认加载config/elasticsearch.yml配置，任何配置在配置文件中能配置的，也能在命令行中配置
    使用-E语法
     -Ehttp.host=0.0.0.0 (默认所有主机都能访问)
     # Allow HTTP API connections from anywhere
     # Connections are encrypted and require user authentication
2. 为了方便开发，关闭安全功能
    -Expack.security.enabled=false
3. 后台运行 
    ./bin/elasticsearch -d -p pid -Expack.security.enabled=false
4. 浏览器访问测试 ES 是否在运行
    http://192.168.1.205:9200/
5. 停止后台运行的 ES
    pkill -F pid
```

> 安装 shasum 命令，`yum install -y perl-Digest-SHA`
> The Elasticsearch .tar.gz package does not include the systemd module. To manage Elasticsearch as a service, use the Debian or RPM package instead.
> http_ca.crt证书可以用来安全连接ES，$ES_HOME/config/certs/http_ca.crt，拷贝到客户端机器

## 开发语言 

Java