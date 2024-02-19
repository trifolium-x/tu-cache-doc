---
description: 这个文档介绍在您的项目中如何使用tu-cache
---

# 😀 如何使用

#### 使用tu-cache

1.  使用tu-cache对service中的方法返回的数据进行缓存

    ```java
    @TuCache("test_service:getList")
    public List<String> getList(){
        return Arrays.asList("tu","nan");
    }
    ```
2.  使用tu-cache删除缓存中的数据

    ```java
    @TuCacheClear("test_service:getList")
    public void delList(){
    }
    ```
3. @TuCache参数
   * `String key() default ""` 缓存的字符串格式key,支持spEl表达式(使用#{}包裹spEl表达式)，默认值为方法签名
   * `long expire() default -1` 缓存的过期时间，单位(秒),默认永不过期. (**在1.0.4**.**RELEASE及以上版本中建议使用 `timeout`**)
   * `boolean resetExpire() default false` 每次获取数据是否重置过期时间.
   * `TimeUnit timeUnit() default TimeUnit.SECONDS` 缓存的时间单位.
   * `String condition() default "true"` 扩展的条件过滤，值为spEl表达式(直接编写表达式不需要使用#{}方式声明为spEl)
   *   样例:

       ```java
       @TuCache(key="test_service:getList:#{#endStr}", expire = 10, timeUnit=TimeUnit.SECONDS)
       public List<String> getList(String endStr){
           return Arrays.asList("tu","nan",endStr);
       }

       // 如果需要当前对象的的方法
       @TuCache(key="test_service:getList:#{#this.endStr()}",expire = 120)
       public List<String> getList(){
           return Arrays.asList("tu","nan",endStr());
       }

       // 使用springBean, (使用安全访问符号?.，可以规避null错误，具体用法请查看spEl表达式)
       @TuCache(key="test_service:getList:#{@springBean.endStr()}",expire = 120)
       public List<String> springBeanGetList(){
           return Arrays.asList("tu","nan",springBean.endStr());
       }

       // 使用condition,当name的长度>=5时进行缓存
       @TuCache(key="test_service:getList:#{#name}", condition="#name.length() >= 5")
       public List<String> springBeanGetList(String name){
           return Arrays.asList("tu","nan",name);
       }

       public String endStr(){
         return "end";
       }
       ```
4. @TuCacheClear参数
   * `String[] key() default {}` 删除的key数组，支持spEl表达式(使用#{}包裹spEl表达式)
   * `String[] keys() default {}` 模糊删除的缓存key数组,支持spEl表达式(使用#{}包裹spEl表达式),对应redis中**deleteKeys**("test\_service:")
   * `boolean async() default false` 是否异步删除，无需等待删除的结果
   * `String condition() default "true"` 扩展的条件过滤，值为spEl表达式(直接编写表达式不需要使用#{}方式声明为spEl)
   *   样例:

       ```java
       @TuCacheClear(key={"test_service:itemDetail:#{#id}"})
       public void deleteItem(Long id){
       }

       // 如果需要调用本地的方法
       @TuCacheClear(keys={"test_service:itemList:","test_service:itemDetail:#{#id}"}, async = true)
       public void deleteItem(Long id){
       }
       ```
   * _注意key和keys的区别_
5. condition 的用法
   * condition要求spEL返回一个boolean类型的值，例如：
     * condition = "#param.startsWith('a')"
     * condition = "false"

#### 更多样例

[https://github.com/trifolium-x/tutu-cache/tree/master/example](https://github.com/trifolium-wang/tutu-cache/tree/master/example)

