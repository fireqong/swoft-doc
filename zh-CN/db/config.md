# 数据库配置

主要是配置数据库主从连接信息，Swoft 提供 `properties` 和 `env` 两种方式配置，且 `env` 会覆盖 `properties` 配置。

> 主从都配置，默认读操作使用从配置，写操作使用主配置. 若**只配置主**，读写操作都会使用主配置

## 一些说明

- 数据库实例: 实例相当于分类，如下面看到的含有默认的两个节点 `master` `slave`, 属于默认实例 `default`
- 数据库节点: 每个实例下的item，都是一个节点，key 是节点名称。 通常我们会用两个节点，分别命名为 `master` `slave`
- 每个节点都会创建一个连接池，池的名称是 `instance.node` 例如下面的 `default.master` `other.master`
  - 通过 `\Swoft::getPool('instance.node')` 可以拿到连接池对象

> 您可以自定义实例和节点的名称，不过使用时要注意区分和选择。当然，我们推荐使用通用的命名

## 关于uri的解释

系统会调用parse_url解析uri字段, 解析出来的host为mysql主机地址, port为端口号, path为数据库名, query是用户名和数据库以及指定编码等.
以上述为例, '127.0.0.1'为mysql主机地址, '3306'为端口号, test为数据库名.

## properties

配置 `config/properties/db.php`

```php
return [
    'master' => [
        'name'        => 'master',
        'uri'         => [
            '127.0.0.1:3306/test?user=root&password=123456&charset=utf8',
            '127.0.0.1:3306/test?user=root&password=123456&charset=utf8',
        ],
        'minActive'   => 8,
        'maxActive'   => 8,
        'maxWait'     => 8,
        'timeout'     => 8,
        'maxIdleTime' => 60,
        'maxWaitTime' => 3,
    ],
    'slave' => [
        'name'        => 'slave',
        'uri'         => [
            '127.0.0.1:3306/test?user=root&password=123456&charset=utf8',
            '127.0.0.1:3306/test?user=root&password=123456&charset=utf8',
        ],
        'minActive'   => 8,
        'maxActive'   => 8,
        'maxWait'     => 8,
        'timeout'     => 8,
        'maxIdleTime' => 60,
        'maxWaitTime' => 3,
    ],
];
```

- master/slave 主从配置
- name 连接池节点名称，用于服务发现
- uri 连接地址信息
- minActive 最小活跃链接数
- maxActive 最大活跃连接数
- maxIdleTime 连接最大空闲时间，单位秒
- maxWaitTime 连接最大等待时间，单位秒
- maxWait 最大等待连接
- timeout 超时时间，单位秒

> master,slave 是两个特殊的名称，他们会归纳到 `default` 实例中去。表现为 `default.master`, `default.slave`

- 像上面直接写 master,slave 框架会自动将这两个划分到 `default` 实例中去
- 所以这里实际结构该是下面这样的(_允许上面的配置是为了兼容之前的版本_), 新增实例应当遵循这个结构

```php
'default' => [
    'master' => [ // ...],
    'slave' => [ // ...],
]
```

## env

配置.env文件

```
# the pool of master nodes pool
DB_NAME=dbMaster
DB_URI=127.0.0.1:3306/test?user=root&password=123456&charset=utf8,127.0.0.1:3306/test?user=root&password=123456&charset=utf8
DB_MIN_ACTIVE=6
DB_MAX_ACTIVE=10
DB_MAX_WAIT=20
DB_MAX_IDLE_TIME=60
DB_MAX_WAIT_TIME=3
DB_TIMEOUT=200

# the pool of slave nodes pool
DB_SLAVE_NAME=dbSlave
DB_SLAVE_URI=127.0.0.1:3306/test?user=root&password=123456&charset=utf8,127.0.0.1:3306/test?user=root&password=123456&charset=utf8
DB_SLAVE_MIN_ACTIVE=5
DB_SLAVE_MAX_ACTIVE=10
DB_SLAVE_MAX_WAIT=20
DB_SLAVE_MAX_WAIT_TIME=3
DB_SLAVE_MAX_IDLE_TIME=60
DB_SLAVE_TIMEOUT=200
```

- DB/DB_SLAVE_NAME 连接池节点名称，用于服务发现
- DB/DB_SLAVE_URI 连接地址信息
- DB/DB_SLAVE_MIN_ACTIVE 最小活跃链接数
- DB/DB_SLAVE_MAX_ACTIVE 最大活跃连接数
- DB/DB_SLAVE_MAX_IDLE_TIME 连接最大空闲时间，单位秒
- DB/DB_SLAVE_MAX_WAIT_TIME 连接最大等待时间，单位秒
- DB/DB_SLAVE_MAX_WAIT 最大等待连接
- DB/DB_SLAVE_TIMEOUT 超时时间，单位秒

## 数据库实例

上面的配置都是属于默认实例 `default`, 含有两个节点 `master` `slave`

### 增加实例

增加实例需在 `db.php` 增加新的实例配置，如下：

- 新增实例 `other`
- 它同样含有两个节点 `master` `slave`

```php
return [
    // ...
    'other' => [
        'master' => [
            'name'        => 'master2',
            'uri'         => [
                '127.0.0.1:3301',
                '127.0.0.1:3301',
            ],
            'maxIdel'     => 1,
            'maxActive'   => 1,
            'maxWait'     => 1,
            'timeout'     => 1,
        ],

        'slave' => [
            'name'        => 'slave3',
            'uri'         => [
                '127.0.0.1:3301',
                '127.0.0.1:3301',
            ],
            'maxIdel'     => 1,
            'maxActive'   => 1,
            'maxWait'     => 1,
            'timeout'     => 1,
        ],
    ],
];
```

> 注意： 新增实例除了要添加配置外，还需新增相关的 pool配置类，pool类，请参照 `app/Pool` 和 `swoft/db` 的test示例


