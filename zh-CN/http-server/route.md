## HTTP路由

HTTP路由在 `\Swoft\Http\Server\Router\HandlerMapping` 中实现, 配置文件在 `config/beans/base.php`:

```php
'httpRouter'       => [
    'ignoreLastSlash'  => false, // 是否忽略最后一个斜杠，设置false后，/user/index和/user/index/是两个不同的路由
    'tmpCacheNumber' => 1000,// 缓存路由数，最近1000条(缓存到路由对象的，重启后失效，只会缓存动态路由)
    'matchAll'       => '', // 匹配所有，所有请求都会匹配到这个uri或闭包
    'currentGroupPrefix' => '', // 将会给所有的路由设置前缀(例如：/api) !!请谨慎使用!!
],
```

> 由于sowft的路由使用全注解方式，所以具体使用请参看控制器章节

