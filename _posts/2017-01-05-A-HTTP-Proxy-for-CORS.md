---
layout: post
title: "使用Java编写HTTP代理解决跨域资源共享问题"
categories:
  - Web
tags:
  - Proxy
  - CORS
last_modified_at: 2017-01-17 22:31:24 +08:00



---

* content
{:toc}

在写一个本地的Web应用时，遇到了这么一个问题：浏览器默认禁止Ajax访问跨域资源，除非在响应头中有申明，而我觉得想要调用的某网站的接口自然没有允许我的本地应用进行访问。为了解决这个问题，我使用Java EE写了一段代码作为代理，在我的本地应用与远程服务器之间进行转发。

本文在本人新博客的链接：[http://www.myblog.link/2017/01/05/A-HTTP-Proxy-for-CORS/](http://www.myblog.link/2017/01/05/A-HTTP-Proxy-for-CORS/)



## 问题分析

比如我的应用想调用example网站检查是否登陆的接口`https://example.com/user/checkLogInStatus`,由于同源策略，浏览器不允许我这样使用Ajax调用它域的资源。如果这个接口的响应头上有这么一句话：

`access-control-allow-origin: * `

那么他就可以被所有网站的网页在前端直接调用了。把`*`换成某个指定的网站，比如这样：

`access-control-allow-origin: http://example2.com` 

那么http://example2.com 就可以调用它了。然而这样直接调用并不会带上example.com的cookies，除非加上

`access-control-allow-credentials: true`

注意如果加上了这句话，`access-control-allow-origin: *`就不能使用，必须指定某个网站，这是出于安全的考虑。

在我的需求下，我需要本地的Web应用能调用接口，则Host为null（Chrome和Firefox为null，Edge下好像是file？），所以我需要在响应头里加上这两句话： 

`access-control-allow-origin: null ` 
`access-control-allow-credentials: true` 
就行了。


## 编码实现

下面是我具体的Java代码。使用Jsoup把请求转发给远程服务器。

``` java
package servlet;

import java.io.IOException;
import java.util.HashMap;
import java.util.Map;
import java.util.Map.Entry;
import java.util.Set;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.jsoup.Connection.Method;
import org.jsoup.Connection.Response;
import org.jsoup.Jsoup;

//下面一行将访问该应用的所有请求交给这个程序处理，比较老的环境可能不支持
@WebServlet("/*")
public class Proxy extends HttpServlet {
    private static final long serialVersionUID = 1L;
    
    //需要代理访问的主机地址（API所在主机地址）
    private static final String REMOTE_HOST = "http://example.com";
    
    //这份代码所在的网站被部署在服务器的什么路径下
    private static final String LOCAL_PATH = "/proxy";
    
    //默认的请求头
    private static Map<String, String> headers = new HashMap<String, String>();

    public Proxy() {
        super();
        headers.put("Host", "vjudge.net");
        headers.put("Connection", "keep-alive");
        headers.put("Content-Length","0");
        headers.put("Accept", "*/*");
        headers.put("Origin", REMOTE_HOST);
        headers.put("X-Requested-With", "XMLHttpRequest");
        headers.put("User-Agent",
                "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36");
        headers.put("Referer", REMOTE_HOST);
        headers.put("Accept-Encoding", "gzip, deflate, br");
        headers.put("Accept-Language", "zh-CN,zh;q=0.8,en;q=0.6");
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        doIt(request, response, false);
    }
    
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        doIt(request, response, true);
    }

    private void doIt(HttpServletRequest request, HttpServletResponse response, boolean post) throws IOException {
        //下面的两行是重点，有了他们就可以带上Cookies跨域获取资源了
        //第一行允许来源null，也就是本地网页访问这里，第二行允许带上Cookies
        response.addHeader("Access-Control-Allow-Origin", "null");
        response.addHeader("Access-Control-Allow-Credentials", "true");
        
        //客户端传来的Cookies
        Cookie[] clientCookies = request.getCookies();
        //发给服务器的Cookies
        HashMap<String, String> serverCookie = new HashMap<String, String>();
        //客户端传来的数据
        Map<String, String> data = new HashMap<String, String>();
        //发给服务器的数据
        Set<Entry<String, String[]>> para = request.getParameterMap().entrySet();
        //客户端访问的URI
        String uri = request.getRequestURI();
        
        //把客户端传来的Cookies转成jsoup使用的格式准备发给远程服务器
        if (clientCookies != null) {
            for (Cookie clienCookie : clientCookies) {
                String key = clienCookie.getName();
                String value = clienCookie.getValue();
                serverCookie.put(key, value);
            }
        }
        
        //把客户端传来的数据转成jsoup使用的格式准备发给远程服务器
        for (Entry<String, String[]> t : para) {
            String key = t.getKey();
            String value = t.getValue()[0];
            data.put(key, value);
        }
        
        //转发给服务器
        Response serverResponse = Jsoup.connect(uri.replace(LOCAL_PATH, REMOTE_HOST)).headers(headers)
                .cookies(serverCookie).data(data).method(post ? Method.POST : Method.GET)
                .ignoreContentType(true).ignoreHttpErrors(true)
                .execute();
        
        if(serverResponse.statusCode() != 200){
            //出现错误，转发给客户端
            response.sendError(serverResponse.statusCode(),serverResponse.statusMessage());
        } else {
            //服务器传回来的Cookies放入response里
            for (Entry<String, String> t : serverResponse.cookies().entrySet()) {
                String key = t.getKey();
                String value = t.getValue();
                Cookie cookie = new Cookie(key, value);
                cookie.setPath(LOCAL_PATH);
                //我所使用的API，注销是以logout结尾的，我就偷懒直接写死了
                cookie.setMaxAge(uri.endsWith("logout") ? -1 : 60 * 60 * 24 * 365);
                response.addCookie(cookie);
            }
            //返回服务器传来的数据
            response.getWriter().append(serverResponse.body());
        }
    }
}
```

## 部署时遇到的问题

其实这节是个题外话，既然遇到了就简单说一下吧。我要调用的接口的网站使用了Let's Encrypt的HTTPS证书，但是我用的JRE并不认可，所以报错。除了把JRE升级到最新的方法以外，还可以这样解决：

下载[https://letsencrypt.org/certs/lets-encrypt-x3-cross-signed.der ](https://letsencrypt.org/certs/lets-encrypt-x3-cross-signed.der) ，然后进入JDK的JRE下面的bin目录，运行 
`keytool -trustcacerts -keystore ../lib/security/cacerts -storepass changeit -noprompt -importcert -alias lets-encrypt-x3-cross-signed -file path/to/lets-encrypt-x3-cross-signed.der`  
-file后要包括下载的那个文件的路径和文件名。我用Java SE测试的时候调用的好像是普通的JRE，到了Java EE的服务器上调用的缺变成了JDK下的JRE。这可能只代表我个人的情况，大家还是注意下可能有多个JRE的情况。

## 后记

直到我写完这个代理程序，用这个代理跑了好几天，在写这篇文章时准备截张直接调用远程接口被浏览器同源策略阻止的图片的时候，我才发现那个接口竟然是允许跨域调用的(。・\`ω´・) 。它会自动的将访问者的主机填入`access-control-allow-origin` ，我还一直纳闷为什么它会在后面接上自己的域名……果然是自己见识少了，还折腾了那么久，不过也算是有不少收获。

------