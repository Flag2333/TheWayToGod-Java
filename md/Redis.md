# Redis

1. ```java
   /**
        * 获取缓存
        * 
        * @param key 
        * @param expire  过期时间
        * @param clazz
        * @param cacheLoadable 从数据库读取数据的方法
        * @param <T>
        * @return
        */
   public <T> T getCache(String key, int expire, TypeReference<T> clazz, CacheLoadable<T> cacheLoadable) {
           T result = null;
           String value = redisUtils.getString(key);//根据key去缓存中查询
           if (StringUtils.isEmpty(value)) {//如果缓存中没有
               synchronized (this) {//这个key加锁后进行处理
                   //重新从缓存获取一次key
                   //原因：前一个相同的key可能已经从数据库获取了数据并且更新到了缓存
                   value = redisUtils.getString(key);
                   if (!StringUtils.isEmpty(value)) {//如果获取到结果 返回
                       return JSON.parseObject(value, clazz);
                   }
                   result = cacheLoadable.load();//没获取到 从数据库读取结果
                   if (result != null) {//从数据库读取的数据不为空
                       String valueJson = JSON.toJSONString(result);
                       redisUtils.setString(key, valueJson, expire);// 更新到缓存
                   }
               }
           } else {
               result = JSON.parseObject(value, clazz);
           }
           return result;
       }
   ```

2. [Redis使用总结（二、缓存和数据库双写一致性问题）](https://blog.csdn.net/hukaijun/article/details/81010475)

3. ```java
   /**
        * 秒杀 气泡
        */
       public boolean secKillBubble(Integer bubbleId, Integer userId) {
           Jedis jedis = getJedis();
           // 第一层保障 从0自增key1
           Long incr = jedis.incr(RedisConstant.bubbleEntryNum + bubbleId);
           // 给key1设置过期时间
           jedis.expire(RedisConstant.bubbleEntryNum+ bubbleId, RedisConstant.bubble_exp);
           try {
               // 监视key2
               jedis.watch(RedisConstant.bubbleForOne + bubbleId);// watchkeys
               // 如果key1没有被自增过则开启事务
               if (incr <=1) {
                   Transaction tx = jedis.multi();// 开启事务
                   // 设置key2的值为用户id(抢到气泡的用户的id)
                   tx.setex(RedisConstant.bubbleForOne + bubbleId,RedisConstant.bubble_exp, userId.toString());
                   // 第二个人过来发现这个气泡已经被设置了用户id 说明已经被别人秒杀掉了 
                   List<Object> list = tx.exec();// 提交事务，如果此时watchkeys被改动了，则返回null
                   if (list != null) {
                       log.info("气泡秒杀成功!" + ",userId" + userId + "，气泡Id：" + bubbleId);
                       return true;
                   }
               }
               log.info("气泡没有秒杀到" + ",userId" + userId + "，气泡Id：" + bubbleId);
               return false;
           } catch (Exception e) {
               log.error("错误：秒杀气泡" + ",userId" + userId + "，气泡Id：" + bubbleId, e);
               e.printStackTrace();
           } finally {
               jedis.close();
           }
           return false;
       }
   ```

4. [jedis连接池配置](https://blog.csdn.net/xingqibaing/article/details/82754916 )

5. [redis 配置](https://blog.csdn.net/u012613251/article/details/81279060 )

