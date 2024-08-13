[![crates.io](https://img.shields.io/crates/v/spring-redis.svg)](https://crates.io/crates/spring-redis)
[![Documentation](https://docs.rs/spring-redis/badge.svg)](https://docs.rs/spring-redis)

## 依赖

```toml
spring-redis = { version = "0.0.5" }
```

## 配置项

```toml
[redis]
uri = "redis://127.0.0.1/"        # redis 数据库地址
connection_timeout = 10000        # 连接超时时间，单位毫秒
response_timeout = 1000           # 响应超时时间，单位毫秒
number_of_retries = 6             # 重试次数，间隔时间按指数增长
exponent_base = 2                 # 间隔时间指数基数，单位毫秒
factor = 100                      # 间隔时间增长因子，默认100倍增长
max_delay = 60000                 # 最大间隔时间
```

## 组件

配置完上述配置项后，插件会自动注册一个[`Redis`](https://docs.rs/spring-redis/latest/spring_redis/type.Redis.html)连接管理对象。该对象是[`redis::aio::ConnectionManager`](https://docs.rs/redis/latest/redis/aio/struct.ConnectionManager.html)的别名。

```rust
pub type Redis = redis::aio::ConnectionManager;
```

## 提取插件注册的Component

`RedisPlugin`插件为我们自动注册了一个连接管理对象，我们可以使用`Component`从AppState中提取这个连接池，[`Component`](https://docs.rs/spring-web/latest/spring_web/extractor/struct.Component.html)是一个axum的[extractor](https://docs.rs/axum/latest/axum/extract/index.html)。

```rust
async fn list_all_redis_key(Component(mut redis): Component<Redis>) -> Result<impl IntoResponse> {
    let keys: Vec<String> = redis.keys("*").await.context("redis request failed")?;
    Ok(Json(keys))
}
```

完整代码参考[`redis-example`](https://github.com/spring-rs/spring-rs/tree/master/examples/redis-example)