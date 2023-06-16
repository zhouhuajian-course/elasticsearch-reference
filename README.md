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

## Document 文档相关 API

https://www.elastic.co/guide/en/elasticsearch/reference/8.8/docs.html

Document APIs

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

```
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