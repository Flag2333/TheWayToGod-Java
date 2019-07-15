1. 两种配置一种是application.properties,另一种是application.yml,

   - 区别：

   application.properties 配置文件在写的时候要写完整，如：

   ```
   spring.profiles.active=dev
   spring.datasource.data-username=root
   spring.datasource.data-password=root
   ```

   *.properties文件中的中文默认以ISO-8859-1方式编码，因此需要对中文内容进行重新编码 

   ```java
   // 转换编码格式
       public String getTitle3() {
           try {
               return new String(title3.getBytes("ISO-8859-1"), "UTF-8");
           } catch (UnsupportedEncodingException e) {
               e.printStackTrace();
           }
           return title3;
       }
   ```

   在yml 文件中配置的话，写法如下：

   ```
   spring:
     profiles:
       active: prod
     datasource:
       driver-class-name: com.mysql.jdbc.Driver
       url: jdbc:mysql://127.0.0.1:3306/test
       username: root
       password: root
   
   yml 文件在写的时候层次感强，而且少写了代码。所以现在很多人都使用yml配置文件。
   ```

2. ```
   配置文件数据的读取：
   比如我在文件中配置了一个 
   massage:
     data:
       name: qibaoyi // qibaoyi前边有空格
   我在类中想要获取他 需要这样去写：
   @Value("${message.data.name}")
   private String name;
   
   后面你取到变量name 的值就是配置文件中配置的值。
   ```

3. ```
   开发过程中通过MVN切换环境参数。
   大家在打包的时候可以通过mvn -P prod 指定环境参数。但是开发过程中，有MVN提供了profile的配置，我们就可以用他来进行处理。
   ```

4.  [Spring Boot 自定义数据源 DruidDataSource](https://blog.csdn.net/wangmx1993328/article/details/81865153)

