[![crates.io](https://img.shields.io/crates/v/spring-boot.svg)](https://crates.io/crates/spring-boot)
[![Documentation](https://docs.rs/spring-boot/badge.svg)](https://docs.rs/spring-boot)

## 介绍

`spring-boot`是`spring`项目的核心模块，该模块包含了：配置管理、插件管理、组件管理。

所有的插件都需要实现[`Plugin`](https://docs.rs/spring-boot/latest/spring_boot/plugin/trait.Plugin.html)特征。

## 如何编写自己的插件

添加依赖

```toml
spring-boot = { version = "0.0.5" }                  # 该crate中包含了插件trait的定义
serde = { workspace = true, features = ["derive"] }  # 用于解析插件的配置项
```

```rust
struct MyPlugin;

#[async_trait]
impl Plugin for MyPlugin {
    async fn build(&self, app: &mut AppBuilder) {
        // 调用app.get_config::<Config>(self)方法即可获取配置项
        match app.get_config::<Config>(self) {
            Ok(config) => {
                println!("{:#?}", config);
                assert_eq!(config.a, 1);
                assert_eq!(config.b, true);

                // 拿到配置项即可构建相应的组件

            }
            Err(e) => println!("{:?}", e),
        }
    }

    /// 配置项的前缀
    fn config_prefix(&self) -> &str {
        "my-plugin"
    }
}

#[derive(Debug, Deserialize)]
struct Config {
    a: u32,
    b: bool,
}
```

完整代码参考[`plugin-example`](https://github.com/spring-rs/spring-rs/tree/master/examples/plugin-example)，也可以参考自带的其他插件代码。