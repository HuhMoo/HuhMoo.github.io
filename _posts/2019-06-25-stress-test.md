---
layout: post
title: "AWS 리눅스에서 간단하게 CPU 부하 주기"
---

1. 설치 :
>sudo amazon-linux-extras install epel  
>sudo yum install stress  

2. 사용법 :
<pre>
stress -c 코어 수  
</pre>
- 즉, 2 cpu 인 경우 stress -c 1 하면 cpu 사용률 50%

