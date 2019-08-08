---
layout: post
title: "AWS Linux 에서 nginx 로 URL redirect"
---
AWS 에서 URL redirect 방법  
1) Reoute 53 에서 S3 를 활용해 간단히 redirect  
1) nginx 등을 통해 redirect  
  
Route53 사용이 불가능하여 AWS Linux 에 nginx 를 하나 올려 redirect 하기로 함  
***  
ec2 생성 및 SG 설정 등은 생략  
sudo 권한 명시 생략  
***  
1. nginx 설치  
>yum install nginx  

OR  

>amazon-linux-extras install nginx1.12  

1. nginx 설정에 redirect 설정 추가  
>cd /etc/nginx  
>vi nginx.conf  
<pre>
 server {  
        listen       80 default_server;  
        listen       [::]:80 default_server;  
        server_name  _;  
        root         /usr/share/nginx/html;  
  
        # Load configuration files for the default server block.  
        include /etc/nginx/default.d/*.conf;  
  
        location / {  
        }  
  
        error_page 404 /404.html;  
            location = /40x.html {  
        }  
  
        error_page 500 502 503 504 /50x.html;  
            location = /50x.html {  
        }  
          
        return 301 https://www.google.com;  
    }  
      
    server {
            listen       443 ssl http2 default_server;  
            listen       [::]:443 ssl http2 default_server;  
            server_name  _;  
            root         /usr/share/nginx/html;  
      
            ssl_certificate "/etc/pki/nginx/server.crt";  
            ssl_certificate_key "/etc/pki/nginx/private/server.key";  
            ssl_session_cache shared:SSL:1m;  
            ssl_session_timeout  10m;  
            ssl_ciphers HIGH:!aNULL:!MD5;  
            ssl_prefer_server_ciphers on;  
      
            # Load configuration files for the default server block.  
            include /etc/nginx/default.d/*.conf;  
      
            location / {  
            }  
      
            error_page 404 /404.html;  
                location = /40x.html {  
            }  
      
            error_page 500 502 503 504 /50x.html;  
                location = /50x.html {  
            }  
      
            return 301 https://www.google.com;  
        }  
</pre>  

1. SSL 처리를 위한 nginx 사설인증서 설정  
>AWS Linux 사설인증서 생성  
>>cd /etc/pki/nginx  
>>openssl rsa -in server.key.origin -out server.key  
>>openssl req -new -key server.key -out server.csr  
>>openssl x509 -req -days 3650 -in server.csr -signkey server.key -out server.crt  
>>mv server.key private/  

>개인키에서 패스워드 제거  
>>cd private  
>>cp server.key server.key.origin  
>>openssl rsa -in server.key.origin -out server.key  
* AWS의 ACM 에서 발급한 공인 인증서는 외부로 내보내기 불가  
   
4. nginx 구동  
>service nginx start  
