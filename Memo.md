
실시간 종목 등록: 3000
실시간 거래원 등록: 3000

chetime으로는 같은 거 확인했으나
localtime으로는 12, 13시간대에 2건씩 차이 발생...

해당 시간의 max tps를 구해...

cat 1228log1.txt | grep "chetime': 11" | cut -d':' -f5 | cut -d',' -f1 | sort | uniq -c | sort -n

04 6151863(x) 
     6214484
05 6390666

volume 차이
176182

---

2024년 1월 2일

100802 697




