# KOVO 한국배구연맹 V 리그 크롤링


- Selenium을 이용

- 5시즌 동안의 경기당 [ 팀명, 경기날짜, 결과, 이름, 공격종합_성공률, 오픈_성공률, 후위_성공률, 속공_성공률, 퀵오픈_성공률, 서브_성공률, 디그_시도, 디그_성공, 세트_시도, 세트_성공, 블로킹_시도, 블로킹_성공, 범실_범실, 포지션, 득점점유율, 스타팅멤버 ] 을 뽑아냄.


# KOVO 한국배구연맹 V 리그 예측

![image](https://user-images.githubusercontent.com/26592315/164896065-19ffc6a8-cedd-46f9-b07f-bf4f7024ea6f.png)

V 리그 남자부, 여자부 22년 3,4월 경기를 예측 코드


- [ 17/18시즌, 18/19시즌, 19/20시즌, 20/21시즌, 21/22시즌 ]

총 5년치의 데이터를 학습 (21/22시즌 3,4월 데이터 제외)

포지션 별로 해당 성공률을 측정, 

|범위|가중치 점수|
|---|---|
|하위 0 ~ 25% |1점|
|하위 25 ~ 50%|2점|
|상위 25% ~ 50%|3점|
|상위 0 ~ 25%|4점|

선수 별로 등급을 매긴 후, One-hot-Encoding으로 구단별로 상대한 선수들을 학습.

---

# 결과 21/22시즌 3, 4월 경기 예측 (남자부)

- ### 예측 데이터에서 주어진 값 : 구단명(2개), 스타팅멤버 

---

# 현대캐피탈 3, 4월 예측 값

hard Train Accuracy:74.72%
hard Test Accuracy:71.43%
hard 예측값: [1 0 0 1 1 1 1]

knn1 Train Accuracy:70.22%
knn1 Test Accuracy:71.43%
knn1 예측값: [0 0 1 1 0 0 1]

lr Train Accuracy:89.33%
lr Test Accuracy:71.43%
lr 예측값: [1 0 0 1 1 1 1]

dt3 Train Accuracy:70.79%
dt3 Test Accuracy:71.43%
dt3 예측값: [1 0 0 1 1 1 1]

dt5 Train Accuracy:73.03%
dt5 Test Accuracy:71.43%
dt5 예측값: [1 0 0 1 1 1 1]

### 총 7경기 정확도 71.43%

---

# 대한항공 3, 4월 예측 값



soft Train Accuracy:79.12%
soft Test Accuracy:72.73%
soft 예측값: [1 1 1 0 1 1 1 1 1 1 1]

dt3 Train Accuracy:75.82%
dt3 Test Accuracy:72.73%
dt3 예측값: [1 1 1 0 1 1 1 1 1 1 1]

dt5 Train Accuracy:76.92%
dt5 Test Accuracy:72.73%
dt5 예측값: [1 1 1 0 1 1 1 1 1 1 1]

### 총 11경기 정확도 72.73%

---

# KB손해보험 3, 4월 예측 값


hard Train Accuracy:79.53%
hard Test Accuracy:72.73%
hard 예측값: [1 1 1 0 0 0 0 0 0 0 0]

knn2 Train Accuracy:78.36%
knn2 Test Accuracy:72.73%
knn2 예측값: [1 1 1 0 0 0 0 0 0 0 0]

lr Train Accuracy:94.74%
lr Test Accuracy:72.73%
lr 예측값: [1 1 1 0 0 0 0 0 0 0 0]

### 총 11경기 정확도 72.73%

---

# 삼성화재 3, 4월 예측 값


soft Train Accuracy:85.96%
soft Test Accuracy:75.00%
soft 예측값: [0 0 0 0 1 0 1 1]

dt3 Train Accuracy:61.99%
dt3 Test Accuracy:87.50%
dt3 예측값: [0 0 0 0 0 0 0 0]

### 총 8경기 정확도 87.50%

---

# OK금융그룹 3, 4월 예측 값

knn2 Train Accuracy:76.16%
knn2 Test Accuracy:71.43%
knn2 예측값: [0 0 0 1 1 0 0]


dt3 Train Accuracy:59.88%
dt3 Test Accuracy:71.43%
dt3 예측값: [0 0 0 0 0 0 0]

### 총 7경기 정확도 71.43%

---

# 한국전력 3, 4월 예측 값



knn2 Train Accuracy:80.36%
knn2 Test Accuracy:70.00%
knn2 예측값: [0 0 1 0 1 1 0 1 0 0]

lr Train Accuracy:95.24%
lr Test Accuracy:80.00%
lr 예측값: [1 0 1 1 1 1 0 1 0 1]

### 총 10경기 정확도 80.00%

---

# 우리카드 3, 4월 예측 값

hard Train Accuracy:83.71%
hard Test Accuracy:62.50%
hard 예측값: [0 0 0 0 1 1 1 1]

soft Train Accuracy:84.27%
soft Test Accuracy:62.50%
soft 예측값: [0 0 0 0 1 1 1 1]

knn1 Train Accuracy:76.97%
knn1 Test Accuracy:62.50%
knn1 예측값: [0 0 0 0 1 1 1 1]

knn2 Train Accuracy:78.09%
knn2 Test Accuracy:62.50%
knn2 예측값: [0 0 0 0 1 1 1 1]

lr Train Accuracy:91.57%
lr Test Accuracy:62.50%
lr 예측값: [0 0 0 0 1 1 1 1]

### 총 8경기 정확도 62.50%

---