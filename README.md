# Elasticsearch 参考

搜索引擎 Search Engine，简称 ES，类似搜索引擎 Apache Solr，根据 Google Trends 目前热度貌似不如 ES

文档 https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html  
源码 https://github.com/elastic/elasticsearch

Elastic 公司，先 Elasticsearch 成功，再创立 Elastic 公司。  
附加产品 Logstash Kibana Beats，一同组成 Elastic Stack。

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
6. 测试 默认端口 9200，默认需要https， 
    (未测试)curl --cacert $ES_HOME/config/certs/http_ca.crt -u elastic https://localhost:9200 
    证书都在config/certs里面
5. 停止 ES，Ctrl-C
```

优化

```text
1. 为了方便开发，让所有主机都能访问
2. 为了方便开发，关闭安全功能
3. 后台运行 ./bin/elasticsearch -d -p pid
4. 浏览器访问测试 ES 是否在运行
5. 停止后台运行的 ES 
```

> 安装 shasum 命令，`yum install -y perl-Digest-SHA`

## 开发语言 

Java