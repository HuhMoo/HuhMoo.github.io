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

1. 싫으면 공인 인증서 사용  
- 아마존 가이드  
https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/SSL-on-amazon-linux-2.html
- 유명 무료 공인 인증서 Let's Ecryption  
설치를 위한 epel 설정
>sudo wget -r --no-parent -A 'epel-release-*.rpm' http://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/  
>sudo rpm -Uvh dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/epel-release-*.rpm  
>sudo yum-config-manager --enable epel*  

다음과 같이 간단하게 사용 가능
>nginx 인 경우 : sudo yum install python2-certbot-nginx  
>apache 인 경우 : sudo yum install -y certbot python2-certbot-apache  
>sudo certbot  
>-> 이메일 입력, 약관 동의 등 진행  

실행 결과
<pre>
Congratulations! Your certificate and chain have been saved at:  
/etc/letsencrypt/live/example.com/fullchain.pem  
Your key file has been saved at:  
/etc/letsencrypt/live/example.com/privkey.pem  
Your cert will expire on 2019-11-07. To obtain a new or tweaked  
version of this certificate in the future, simply run certbot again  
with the "certonly" option. To non-interactively renew *all* of  
your certificates, run "certbot renew"  
</pre>  

웹 서버 설정 파일을 확인해보면 인증서 파일 위치가 자동으로 바뀌어 있음  
> cat nginx.conf | grep ssl_certificate  
<pre>
ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem; # managed by Certbot  
ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem; # managed by Certbot  
</pre>  

단, 3개월 마다 갱신 필요  
>sudo certbot renew  
