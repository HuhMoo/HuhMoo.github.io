---
layout: post
title: "SSL 통신을 위한 사설 인증서 만들기"
---
1. SSL 처리를 위한 사설인증서 : key, csr, crt 파일 생성  
>openssl rsa -in server.key.origin -out server.key  
>openssl req -new -key server.key -out server.csr  
>openssl x509 -req -days 3650 -in server.csr -signkey server.key -out server.crt    

1. nginx 나 apache 를 위한 개인키 패스워드 제거  
>cd private  
>cp server.key server.key.origin  
>openssl rsa -in server.key.origin -out server.key    

1. 브라우저를 통한 접속 시 사설 인증서의 한계  
<pre>
"연결이 비공개로 설정되어 있지 않습니다."  
"이 사이트는 안전하지 않습니다."  
</pre>  
- 싫으면 공인 인증서 사용  