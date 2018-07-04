# 加密

X-Pack虽然开源，但是加密功能依旧收费，我们选择用[search-guard](https://search-guard.com/)的免费版本实现

## 下载

打开下载地址，点击 zip，文件名类似：`search-guard-6-6.3.0-compliance-2.zip`
```
http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.floragunn%22%20AND%20a%3A%22search-guard-6%22
```

## 关闭碎片分配

此步骤是可选的，但特别推荐用于具有大量数据的大型群集。此步骤可确保在重新启动群集时不会移动分片，从而导致大量`I/O 消耗`。 在任意一台执行即可，可以`PUT请求`下面的`JSON`。
```
$ curl -Ss -XPUT 'https://localhost:9200/_cluster/settings?pretty' \ 
  -H 'Content-Type: application/json' -d'
{
  "persistent": {
    "cluster.routing.allocation.enable": "none"
  }
}
'
```

