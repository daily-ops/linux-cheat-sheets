# Redirection

## Follow redirection

Web servers often configure endpoints that are not designed to serve initial request from client to redirect to a main page and it may display similar output below when using `curl` command. 

```
curl http://127.0.0.1:8200
<a href="/ui/">Temporary Redirect</a>.
```

Looking into the verbosed output, the server replied with HTTP code 307 along with the `Location` in the header to instruct client that it could find the right spot there.

```
curl -v http://127.0.0.1:8300/ | more
*   Trying 127.0.0.1:8300...
* TCP_NODELAY set
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0* Connected to 127.0.0.1 (127.0.0.1) port 8300 (#0)
> GET / HTTP/1.1
> Host: 127.0.0.1:8300
> User-Agent: curl/7.68.0
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 307 Temporary Redirect
< Cache-Control: no-store
< Content-Type: text/html; charset=utf-8
< Location: /ui/
< Strict-Transport-Security: max-age=31536000; includeSubDomains
< Date: Mon, 28 Oct 2024 05:37:20 GMT
< Content-Length: 40
< 
{ [40 bytes data]
100    40  100    40    0     0  40000      0 --:--:-- --:--:-- --:--:-- 40000
* Connection #0 to host 127.0.0.1 left intact
<a href="/ui/">Temporary Redirect</a>.
```

The only thing required for this is to add `-L` to the curl command to follow the redirection.

```
curl -vL http://127.0.0.1:8300 | more
*   Trying 127.0.0.1:8300...
* TCP_NODELAY set
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0* Connected to 127.0.0.1 (127.0.0.1) port 8300 (#0)
> GET / HTTP/1.1
> Host: 127.0.0.1:8300
> User-Agent: curl/7.68.0
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 307 Temporary Redirect
< Cache-Control: no-store
< Content-Type: text/html; charset=utf-8
< Location: /ui/
< Strict-Transport-Security: max-age=31536000; includeSubDomains
< Date: Mon, 28 Oct 2024 05:40:54 GMT
< Content-Length: 40
< 
* Ignoring the response-body
{ [40 bytes data]
100    40  100    40    0     0  40000      0 --:--:-- --:--:-- --:--:-- 40000
* Connection #0 to host 127.0.0.1 left intact
* Issue another request to this URL: 'http://127.0.0.1:8300/ui/'
* Found bundle for host 127.0.0.1: 0x55bc015cd070 [serially]
* Can not multiplex, even if we wanted to!
* Re-using existing connection! (#0) with host 127.0.0.1
* Connected to 127.0.0.1 (127.0.0.1) port 8300 (#0)
> GET /ui/ HTTP/1.1
> Host: 127.0.0.1:8300
> User-Agent: curl/7.68.0
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Accept-Ranges: bytes
```
