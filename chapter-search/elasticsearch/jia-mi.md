# Search Guard

X-Pack虽然开源，但是加密功能依旧收费，我们选择用[search-guard](https://search-guard.com/)的免费版本实现



## X 检查状态

```
$ curl -XGET 'http://127.0.0.1:9200/_searchguard/health'
```

返回如下内容，即表示正常
```
{
  message: null,
  mode: "strict",
  status: "UP"
}
```

