---
layout: post
title: "AWS EBS 용량 증설 방법"
---
일단 콘솔에서 EBS 용량을 변경한다. (예: 8 -> 16)  
  
1. 현재 용량 확인  
<pre>
[ec2-user@ip-123-1-2-3 ~]$ df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        3.8G     0  3.8G   0% /dev
tmpfs           3.8G     0  3.8G   0% /dev/shm
tmpfs           3.8G  456K  3.8G   1% /run
tmpfs           3.8G     0  3.8G   0% /sys/fs/cgroup
/dev/nvme0n1p1  8.0G  6.6G  1.4G  83% /
tmpfs           769M     0  769M   0% /run/user/1000
</pre>
- / 에 아직 8G 만 표시되어 있음

1. 블럭 정보 확인 
<pre>
[ec2-user@ip-123-1-2-3 ~]$ lsblk
NAME          MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
nvme0n1       259:0    0  16G  0 disk
├─nvme0n1p1   259:1    0   8G  0 part /
└─nvme0n1p128 259:2    0   1M  0 part
</pre>
- 16G 로 용량을 증설하였으나 기존과 같이 8G 만 할당되어 있음  

1. 블럭 용량 증설
<pre>
[ec2-user@ip-123-1-2-3 ~]$ sudo growpart /dev/nvme0n1 1
CHANGED: partition=1 start=4096 old: size=16773087 end=16777183 new: size=33550303,end=33554399

[ec2-user@ip-123-1-2-3 ~]$ lsblk
NAME          MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
nvme0n1       259:0    0  16G  0 disk
├─nvme0n1p1   259:1    0  16G  0 part /
└─nvme0n1p128 259:2    0   1M  0 part
</pre>

1. 현재 용량 확인
<pre>
[ec2-user@ip-123-1-2-3 ~]$ df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        3.8G     0  3.8G   0% /dev
tmpfs           3.8G     0  3.8G   0% /dev/shm
tmpfs           3.8G  456K  3.8G   1% /run
tmpfs           3.8G     0  3.8G   0% /sys/fs/cgroup
/dev/nvme0n1p1  8.0G  6.6G  1.4G  83% /
tmpfs           769M     0  769M   0% /run/user/1000
</pre>
- 증설된 블럭 용량이 아직 파일 시스템 상에는 할당이 안되어 있음

1. XFS file system 설정 프로그램 설치
<pre>
[ec2-user@ip-123-1-2-3 ~]$ sudo yum install xfsprogs
Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
amzn2-core                                                                                                                                                                              | 2.4 kB  00:00:00
amzn2extra-docker                                                                                                                                                                       | 1.3 kB  00:00:00
Package xfsprogs-4.5.0-18.amzn2.0.1.x86_64 already installed and latest version
Nothing to do
</pre>

1. / 에 용량 재할당
<pre>
[ec2-user@ip-123-1-2-3 ~]$ sudo xfs_growfs -d /
meta-data=/dev/nvme0n1p1         isize=512    agcount=4, agsize=524159 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1 spinodes=0
data     =                       bsize=4096   blocks=2096635, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal               bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 2096635 to 4193787
</pre>

1. 현재 용량 확인
<pre>
[ec2-user@ip-123-1-2-3 ~]$ df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        3.8G     0  3.8G   0% /dev
tmpfs           3.8G     0  3.8G   0% /dev/shm
tmpfs           3.8G  456K  3.8G   1% /run
tmpfs           3.8G     0  3.8G   0% /sys/fs/cgroup
/dev/nvme0n1p1   16G  6.6G  9.4G  42% /
tmpfs           769M     0  769M   0% /run/user/1000
</pre>

끝~~

