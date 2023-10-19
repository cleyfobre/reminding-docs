
#devops #disk #aws #ec2 #ebs

1. df -h 에서 루트 파일시스템 확인
2. lsblk 로 EBS 용량 늘어난것 확인
3. sudo growpart /dev/xvda 1 (df -h 에서 나온 루트 파일시스템명)  으로 파일시스템 스토리지 확장 
```
ubuntu@ip-172-11-1-111:~$ lsblk
NAME     MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
loop1      7:1    0  55.7M  1 loop /snap/core18/2790
loop2      7:2    0  63.3M  1 loop
loop3      7:3    0 111.9M  1 loop /snap/lxd/24322
loop4      7:4    0  49.8M  1 loop
loop5      7:5    0  55.7M  1 loop /snap/core18/2785
loop6      7:6    0  24.8M  1 loop /snap/amazon-ssm-agent/6563
loop7      7:7    0  40.8M  1 loop /snap/snapd/19993
loop8      7:8    0  63.4M  1 loop /snap/core20/1974
loop9      7:9    0  53.3M  1 loop /snap/snapd/19457
loop10     7:10   0  24.9M  1 loop /snap/amazon-ssm-agent/7628
loop11     7:11   0  63.5M  1 loop /snap/core20/2015
xvda     202:0    0    30G  0 disk
├─xvda1  202:1    0   7.9G  0 part /
├─xvda14 202:14   0     4M  0 part
└─xvda15 202:15   0   106M  0 part /boot/efi
```

4. sudo resize2fs /dev/xvda1 으로 내부 파일시스템 용량 리사이징