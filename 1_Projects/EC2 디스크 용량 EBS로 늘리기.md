
#devops #disk #ec2

1. df -h 에서 루트 파일시스템 확인
2. lsblk 로 EBS 용량 늘어난것 확인
3. sudo growpart /dev/xvda 1 (df -h 에서 나온 루트 파일시스템명)  으로 파일시스템 스토리지 확장
4. sudo resize2fs /dev/xvda1 으로 내부 파일시스템 용량 리사이징