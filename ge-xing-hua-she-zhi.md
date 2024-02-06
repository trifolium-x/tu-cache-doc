---
description: 如何配置tu-cache，定制化
---

# 🧐 个性化设置

#### 个性化设置

* tutu-cache默认提供了 RedisTuCacheService,如果用户使用的缓存是redis并配置了redisTemplate的bean则自动使用该默认缓存服务。
* 用户使用其他缓存，则需要自定义TuCacheService，实现该接口并注入到TuCacheBean中
* 在SpringBoot中在Configure类中配置相应的bean自动使用自定义的bean
* 如果用户需要每个缓存前面添加同意的keyPrefix，TuCacheBean的prefixKey参数
*   springBoot中配置

    ```yaml
    tucache:
      enabled: true
      cache-type: redis
      profiles:
        cache-prefix: "my_tu_key_test:"
        pool:
          core-thread-num: 2
        enable-debug-log: true
        # ...
    ```
*   springMVC中注入到TuCacheBean

    ```xml
    <bean id="tuCacheProfiles" class="co.tunan.tucache.core.config.TuCacheProfiles">
        <property name="cachePrefix" value="test_tucache_prefixkey:" />
    </bean>
    ```

    ```xml
    <bean id="tuCacheBean" class="co.tunan.tucache.core.aspect.TuCacheAspect">
        <property name="tuCacheService" ref="redisCacheService" />
        <property name="tuCacheProfiles" ref="tuCacheProfiles" />
    </bean>
    ```
* 关于默认RedisTuCacheService的序列化问题，强烈建议使用对key使用String方式序列化
*   使用Json序列化配置样例如下:

    ```java
    @Bean(name = "redisTemplate")
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashValueSerializer(new GenericJackson2JsonRedisSerializer(createGenericObjectMapper()));
        redisTemplate.setValueSerializer(new GenericJackson2JsonRedisSerializer(createGenericObjectMapper()));

        redisTemplate.setConnectionFactory(redisConnectionFactory);

        return redisTemplate;
    }
    ```
