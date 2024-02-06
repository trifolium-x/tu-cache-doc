---
description: 快速在spring boot或者spring中使用tu-cache
---

# 🥳 Quick Start

### 安装

1. 在springBoot中的安装
   *   引入jar依赖包

       ```xml
         <dependency>
             <groupId>co.tunan.tucache</groupId>
             <artifactId>tucache-spring-boot-starter</artifactId>
             <version>1.0.4</version>
         </dependency>
         <dependency>
             <groupId>org.springframework.boot</groupId>
             <artifactId>spring-boot-starter-data-redis</artifactId>
         </dependency>
         <!-- 或者其他缓存 -->
       ```
   *   在Configure类中注册javaBean redisTemplate或者使用默认的redisTemplate，必须开启aspectj的aop功能(默认是开启的)

       ```java
       @Bean(name = "redisTemplate")
       public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
             RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
             redisTemplate.setKeySerializer(new StringRedisSerializer());
             redisTemplate.setValueSerializer(new GenericJackson2JsonRedisSerializer());
             redisTemplate.setConnectionFactory(redisConnectionFactory);

             return redisTemplate;
       }
       ```
2. 在springMVC中的安装
   *   引入jar依赖包

       ```xml
       <dependency>
         <groupId>co.tunan.tucache</groupId>
         <artifactId>cache-core</artifactId>
         <version>1.0.4</version>
       </dependency>
       ```
   *   在applicationContent.xml中配置

       ```xml
       <!-- 如果使用的缓存是redis则注入默认实现的RedisCacheService -->
       <bean id="redisCacheService" class="co.tunan.tucache.core.cache.impl.RedisCacheService">
           <property name="redisTemplate" ref="redisTemplate" />
       </bean>

       <bean id="tuCacheAspect" class="co.tunan.tucache.core.aspect.TuCacheAspect">
           <property name="tuCacheService" ref="redisCacheService" />
       </bean>

       <!-- 注意项目中需要引入spring-aop和spring-data-redis的相关依赖 -->
       ```
