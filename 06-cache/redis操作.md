redis

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



