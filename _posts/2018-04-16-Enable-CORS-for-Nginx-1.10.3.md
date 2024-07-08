---
layout: post
title: "Nginx1.10.3启用CORS"
categories:
  - Web
tags:                                            
  - CORS
last_modified_at: 2019-05-25 15:44:56 +08:00
---



该配置在 Nginx 1.10.3 环境下，对于我的部署环境中的 RESTful API 的各种 HTTP Methods 有效，不只是 GET。在Nginx配置文件对应的`location`里面加入如下代码：





```nginx
set $cors '';
# 下面这行修改成对自己需要的合法来源的判断
if ($http_origin ~ '^https?://(localhost|www\.yourdomain\.com|www\.yourotherdomain\.com)') {
	set $cors 'true';
}

if ($cors = 'true') {
	add_header 'Access-Control-Allow-Origin' "$http_origin" always;
	add_header 'Access-Control-Allow-Credentials' 'true' always;
	add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, DELETE, OPTIONS' always;
	add_header 'Access-Control-Allow-Headers' 'Accept,Authorization,Cache-Control,Content-Type,DNT,If-Modified-Since,Keep-Alive,Origin,User-Agent,X-Requested-With' always;
	# required to be able to read Authorization header in frontend
	# add_header 'Access-Control-Expose-Headers' 'Authorization' always;
}

if ($request_method = 'OPTIONS') {
	add_header 'Access-Control-Allow-Origin' "$http_origin" always;
	add_header 'Access-Control-Allow-Credentials' 'true' always;
	add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, DELETE, OPTIONS' always;
	add_header 'Access-Control-Allow-Headers' 'Accept,Authorization,Cache-Control,Content-Type,DNT,If-Modified-Since,Keep-Alive,Origin,User-Agent,X-Requested-With' always;
	# Tell client that this pre-flight info is valid for 20 days
	add_header 'Access-Control-Max-Age' 1728000;
	add_header 'Content-Type' 'text/plain charset=UTF-8';
	add_header 'Content-Length' 0;
	return 204;
}
```

该配置文件参考了[https://gist.github.com/Stanback/7145487](https://gist.github.com/Stanback/7145487)，有修改。