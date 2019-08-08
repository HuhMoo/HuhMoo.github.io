---
layout: post
title: "AWS EC2 에서 Permission denied (publickey) 이슈"
---
현상 :  
EC2 에서 다른 EC2 접속이 다음과 같이 안 됨  
<pre>
Permission denied (publickey)  
</pre>  
***  
해결 방법 :   
1. xxx.pem 파일을 chmod 600 해본다  
1. 그래도 안되면 ec2-user@ip 와 같이 계정명 명시.  
1. 그래도 안되면 맞는 계정인지 확인. (os 가 달라 ubuntu@ip 인 경우 등)  
