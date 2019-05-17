# Example of NGINX nested locations

This is a small repo if you want to play with how nginx handles different location logic.

proxy_pass is not passed in a nested location.

> https://forum.nginx.org/read.php?2,243488,243493#msg-243493

http://nginx.org/en/docs/http/ngx_http_core_module.html#location

By design the longest matching location will be the one that nginx will follow.

> If there are several matching location blocks nginx selects the one with the longest prefix.
> http://nginx.org/en/docs/beginners_guide.html

And a prefix is `/` in `location /`.

## How to use it

```
docker-compose up
```

Use httpie, try the root, you will see a typically success Apache website

```
$ http :8080
HTTP/1.1 200 OK
Accept-Ranges: bytes
Connection: keep-alive
Content-Length: 45
Content-Type: text/html
Date: Fri, 17 May 2019 16:21:05 GMT
ETag: "2d-432a5e4a73a80"
Last-Modified: Mon, 11 Jun 2007 18:53:14 GMT
Server: nginx/1.15.12

<html><body><h1>It works!</h1></body></html>
```

Then try /status and see that it is forbidden

```
$ http :8080/status
HTTP/1.1 403 Forbidden
Connection: keep-alive
Content-Length: 154
Content-Type: text/html
Date: Fri, 17 May 2019 16:17:18 GMT
Server: nginx/1.15.12

<html>
<head><title>403 Forbidden</title></head>
<body>
<center><h1>403 Forbidden</h1></center>
<hr><center>nginx/1.15.12</center>
</body>
</html>
```

Then try to add your own IP to the config, you can find it in the nginx logs from docker-compose up.

```
allow 172.18.0.1;
```

Then do a `docker-compose up` again and then try the same request

```
$ http :8080/status
HTTP/1.1 404 Not Found
Connection: keep-alive
Content-Length: 204
Content-Type: text/html; charset=iso-8859-1
Date: Fri, 17 May 2019 16:25:04 GMT
Server: nginx/1.15.12

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>404 Not Found</title>
</head><body>
<h1>Not Found</h1>
<p>The requested URL /status was not found on this server.</p>
</body></html>
```

Now we don't get a 403, but a typically 404 from apache.
