### redis删除指定前缀的key

```shell
redis-cli keys "key*" | xargs redis-cli del
```

```php
$pipe = 
Redis::pipeline(function ($pipe) {
    for ($i = 0; $i < 1000; $i++) {
        $pipe->set("key:$i", $i);
    }
});
```

### redis删除所有仓库的数据

```
flushall
```

