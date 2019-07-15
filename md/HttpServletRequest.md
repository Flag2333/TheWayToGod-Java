1. [javaweb学习总结(十)——HttpServletRequest对象(一)](https://www.cnblogs.com/xdp-gacl/p/3798347.html)

   -  获得客户机信息

     **getRequestURL方法返回客户端发出请求时的完整URL。** 　　

     **getRequestURI方法返回请求行中的资源名部分。** 　　

     **getQueryString 方法返回请求行中的参数部分。** 　　

     getPathInfo方法返回请求URL中的额外路径信息。额外路径信息是请求URL中的位于Servlet的路径之后和查询参数之前的内容，它以“/”开头。 　　

     **getRemoteAddr方法返回发出请求的客户机的IP地址。** 　　

     getRemoteHost方法返回发出请求的客户机的完整主机名。 　　

     getRemotePort方法返回客户机所使用的网络端口号。 　　

     getLocalAddr方法返回WEB服务器的IP地址。 　　

     getLocalName方法返回WEB服务器的主机名。 