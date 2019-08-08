---
layout: post
title: "Mysql USER 추가 후 DB 권한 설정"
---
계정 생성  
> CREATE user '유저아이디'@'%' IDENTIFIED BY '패스워드';  
  
권한 부여  
> grant all privileges ON 데이터베이스.* to '유저아이디' identified BY '패스워드' with grant OPTION  
