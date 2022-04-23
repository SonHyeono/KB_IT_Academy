# 모듈 import


```python
from selenium import webdriver
from bs4 import BeautifulSoup
import time
import pandas as pd
pd.set_option('display.max_columns', None) # 데이터프레임 컬럼 display

import warnings
warnings.filterwarnings('ignore') # 경고 무시
```

<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

# 한 경기의 정보 가져오기
- 크롤링
- 데이터 정리

#### 크롤링
- selenium : 웹 제어 (클릭)
- BeautifulSoup : html에서 값 읽기


```python
from selenium import webdriver
from selenium.webdriver.common.by import By
driver = webdriver.Chrome(executable_path='./chromedriver.exe')
```


```python
# 크롬 브라우저


driver = webdriver.Chrome(executable_path='./chromedriver.exe')

# url 접속
url = "https://www.kovo.co.kr/game/v-league/11141_game-summary.asp?season=017&g_part=201&r_round=1&g_num=2&"
driver.get(url)
# 1초 대기
time.sleep(1)

#--------------------- table 태그 읽기 - 팀 별 1 ~ 5 세트 결과 ---------------------#
html = driver.page_source
soup = BeautifulSoup(html, 'html.parser')
table = soup.find_all('table')
set_result = pd.read_html(str(table))[1] # 1 ~ 5 세트 별 득점 결과
display(set_result)

#--------------------- 날짜 정보 읽기 ---------------------#
date_selector = '#wrp_content > article.wrp_recentgame.wrp_result > table > thead > tr > th'
date = soup.select_one(date_selector).get_text()
date = date.replace('\xa0', '').split('/')[0].strip().split()
date = pd.to_datetime(date[0][:-1] + '/' + date[1][:-1] + '/' + date[2][:-1])

#
# 첫 번째 팀 정보 읽기
#

#--------------------- 버튼 클릭 - 선수 기록 ---------------------#
x_path = '//*[@id="wrp_content"]/article[2]/ul/li[2]/a/span' # 선수 기록 버튼
cursor = driver.find_element_by_xpath(x_path)
cursor.click()
time.sleep(1)

#--------------------- table 태그 읽기 - 선수 별 출전 세트, 득점, 공격 종합 ---------------------#
html = driver.page_source
soup = BeautifulSoup(html, 'html.parser')
table = soup.find_all('table')

team1 = pd.read_html(str(table))[3] # 명단 table
team1_info = pd.read_html(str(table))[4] # 출전 세트, 득점, 공격 종합 table
team1_info.columns = ['_'.join(col).strip() for col in team1_info.columns]
team1 = pd.concat([team1, team1_info], axis = 1)

#--------------------- 나머지 기록 읽기 ---------------------#
for _ in range(4) :
    #--------------------- 버튼 클릭 - 다음 기록 ---------------------#
    x_path = '//*[@id="team1"]/div/div[1]/a[2]' # 다음 기록 버튼
    cursor = driver.find_element_by_xpath(x_path)
    cursor.click()
    time.sleep(1)
    
    
    #--------------------- table 태그 읽기 - 다음 기록들 ---------------------#
    html = driver.page_source
    soup = BeautifulSoup(html, 'html.parser')
    table = soup.find_all('table')
    
    team1_info = pd.read_html(str(table))[4] # 다음 기록
    team1_info.columns = ['_'.join(col).strip() for col in team1_info.columns]
    team1 = pd.concat([team1, team1_info], axis = 1)
display(team1)


#
# 두 번째 팀 정보 읽기
#

#--------------------- 버튼 클릭 - 두 번째 팀 ---------------------#
x_path = '//*[@id="tab2"]/div[2]/ul/li[2]/a/span' # 두 번째 팀 버튼
cursor = driver.find_element_by_xpath(x_path)
cursor.click()
time.sleep(1)

#--------------------- table 태그 읽기 - 선수 별 출전 세트, 득점, 공격 종합 ---------------------#
html = driver.page_source
soup = BeautifulSoup(html, 'html.parser')
table = soup.find_all('table')

team2 = pd.read_html(str(table))[5] # 명단 table
team2_info = pd.read_html(str(table))[6] # 출전 세트, 득점, 공격 종합 table
team2_info.columns = ['_'.join(col).strip() for col in team2_info.columns]
team2 = pd.concat([team2, team2_info], axis = 1)

#--------------------- 나머지 기록 읽기 ---------------------#
for _ in range(4) :
    #--------------------- 버튼 클릭 - 다음 기록 ---------------------#
    x_path = '//*[@id="team2"]/div/div[1]/a[2]' # 다음 기록 버튼
    cursor = driver.find_element_by_xpath(x_path)
    cursor.click()
    time.sleep(1)
    
    
    #--------------------- table 태그 읽기 - 다음 기록들 ---------------------#
    html = driver.page_source
    soup = BeautifulSoup(html, 'html.parser')
    table = soup.find_all('table')
    
    team2_info = pd.read_html(str(table))[6] # 다음 기록
    team2_info.columns = ['_'.join(col).strip() for col in team2_info.columns]
    team2 = pd.concat([team2, team2_info], axis = 1)
display(team2)
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>여자부</th>
      <th>1SET</th>
      <th>2SET</th>
      <th>3SET</th>
      <th>4SET</th>
      <th>5SET</th>
      <th>TOTAL</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>현대건설</td>
      <td>19</td>
      <td>25</td>
      <td>25</td>
      <td>20</td>
      <td>15</td>
      <td>104</td>
    </tr>
    <tr>
      <th>1</th>
      <td>GS칼텍스</td>
      <td>25</td>
      <td>22</td>
      <td>21</td>
      <td>25</td>
      <td>9</td>
      <td>102</td>
    </tr>
    <tr>
      <th>2</th>
      <td>경기시간</td>
      <td>26 m</td>
      <td>33 m</td>
      <td>26 m</td>
      <td>28 m</td>
      <td>15 m</td>
      <td>2h08m</td>
    </tr>
  </tbody>
</table>
</div>



<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>No.</th>
      <th>이름</th>
      <th>출전세트_1set</th>
      <th>출전세트_2set</th>
      <th>출전세트_3set</th>
      <th>출전세트_4set</th>
      <th>출전세트_5set</th>
      <th>득점_득점</th>
      <th>공격종합_시도</th>
      <th>공격종합_성공</th>
      <th>공격종합_공격차단</th>
      <th>공격종합_범실</th>
      <th>공격종합_성공률</th>
      <th>공격종합_점유율</th>
      <th>오픈_시도</th>
      <th>오픈_성공</th>
      <th>오픈_공격차단</th>
      <th>오픈_범실</th>
      <th>오픈_성공률</th>
      <th>오픈_점유율</th>
      <th>시간차_시도</th>
      <th>시간차_성공</th>
      <th>시간차_공격차단</th>
      <th>시간차_범실</th>
      <th>시간차_성공률</th>
      <th>시간차_점유율</th>
      <th>이동_시도</th>
      <th>이동_성공</th>
      <th>이동_공격차단</th>
      <th>이동_범실</th>
      <th>이동_성공률</th>
      <th>이동_점유율</th>
      <th>후위_시도</th>
      <th>후위_성공</th>
      <th>후위_공격차단</th>
      <th>후위_범실</th>
      <th>후위_성공률</th>
      <th>후위_점유율</th>
      <th>속공_시도</th>
      <th>속공_성공</th>
      <th>속공_공격차단</th>
      <th>속공_범실</th>
      <th>속공_성공률</th>
      <th>속공_점유율</th>
      <th>퀵오픈_시도</th>
      <th>퀵오픈_성공</th>
      <th>퀵오픈_공격차단</th>
      <th>퀵오픈_범실</th>
      <th>퀵오픈_성공률</th>
      <th>퀵오픈_점유율</th>
      <th>서브_시도</th>
      <th>서브_성공</th>
      <th>서브_범실</th>
      <th>서브_성공률</th>
      <th>서브_점유율</th>
      <th>디그_시도</th>
      <th>디그_성공</th>
      <th>디그_실패</th>
      <th>디그_범실</th>
      <th>디그_세트당</th>
      <th>디그_점유율</th>
      <th>세트_시도</th>
      <th>세트_성공</th>
      <th>세트_범실</th>
      <th>세트_세트당</th>
      <th>세트_점유율</th>
      <th>리시브_시도</th>
      <th>리시브_정확</th>
      <th>리시브_실패</th>
      <th>리시브_세트당</th>
      <th>리시브_점유율</th>
      <th>블로킹_시도</th>
      <th>블로킹_성공</th>
      <th>블로킹_유효블락</th>
      <th>블로킹_실패</th>
      <th>블로킹_범실</th>
      <th>블로킹_세트당</th>
      <th>블로킹_점유율</th>
      <th>블로킹_어시스트</th>
      <th>벌칙_벌칙</th>
      <th>범실_범실</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10</td>
      <td>루소 (L)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>28</td>
      <td>54</td>
      <td>22</td>
      <td>3</td>
      <td>3</td>
      <td>40.74</td>
      <td>35.29</td>
      <td>27</td>
      <td>10</td>
      <td>3</td>
      <td>2</td>
      <td>37.04</td>
      <td>29.35</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>12</td>
      <td>7</td>
      <td>0</td>
      <td>0</td>
      <td>58.33</td>
      <td>92.31</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>15</td>
      <td>5</td>
      <td>0</td>
      <td>1</td>
      <td>33.33</td>
      <td>51.72</td>
      <td>16</td>
      <td>2</td>
      <td>3</td>
      <td>0.4</td>
      <td>15.38</td>
      <td>11</td>
      <td>8</td>
      <td>3</td>
      <td>0</td>
      <td>1.60</td>
      <td>10.28</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>1.89</td>
      <td>12</td>
      <td>2</td>
      <td>3</td>
      <td>0.00</td>
      <td>13.19</td>
      <td>15</td>
      <td>4</td>
      <td>2</td>
      <td>5</td>
      <td>0</td>
      <td>0.8</td>
      <td>19.48</td>
      <td>2</td>
      <td>0</td>
      <td>6</td>
    </tr>
    <tr>
      <th>1</th>
      <td>13</td>
      <td>정지윤 (C)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>21</td>
      <td>34</td>
      <td>20</td>
      <td>3</td>
      <td>1</td>
      <td>58.82</td>
      <td>22.22</td>
      <td>26</td>
      <td>16</td>
      <td>2</td>
      <td>1</td>
      <td>61.54</td>
      <td>28.26</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>5</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>40.0</td>
      <td>27.78</td>
      <td>3</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>66.67</td>
      <td>10.34</td>
      <td>9</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>8.65</td>
      <td>10</td>
      <td>8</td>
      <td>2</td>
      <td>0</td>
      <td>1.60</td>
      <td>9.35</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>0.2</td>
      <td>1.26</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>15</td>
      <td>1</td>
      <td>5</td>
      <td>4</td>
      <td>0</td>
      <td>0.2</td>
      <td>19.48</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>14</td>
      <td>양효진 (C)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>18</td>
      <td>32</td>
      <td>15</td>
      <td>2</td>
      <td>2</td>
      <td>46.88</td>
      <td>20.92</td>
      <td>20</td>
      <td>9</td>
      <td>1</td>
      <td>0</td>
      <td>45.00</td>
      <td>21.74</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>12</td>
      <td>6</td>
      <td>1</td>
      <td>2</td>
      <td>50.0</td>
      <td>66.67</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>21</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>20.19</td>
      <td>10</td>
      <td>7</td>
      <td>3</td>
      <td>0</td>
      <td>1.40</td>
      <td>9.35</td>
      <td>3</td>
      <td>2</td>
      <td>0</td>
      <td>0.4</td>
      <td>1.89</td>
      <td>3</td>
      <td>1</td>
      <td>0</td>
      <td>33.33</td>
      <td>3.30</td>
      <td>13</td>
      <td>3</td>
      <td>2</td>
      <td>5</td>
      <td>0</td>
      <td>0.6</td>
      <td>16.88</td>
      <td>5</td>
      <td>0</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>17</td>
      <td>고예림 (L)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>8</td>
      <td>19</td>
      <td>6</td>
      <td>3</td>
      <td>2</td>
      <td>31.58</td>
      <td>12.42</td>
      <td>9</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>22.22</td>
      <td>9.78</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>7.69</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>8</td>
      <td>3</td>
      <td>1</td>
      <td>2</td>
      <td>37.50</td>
      <td>27.59</td>
      <td>15</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>14.42</td>
      <td>16</td>
      <td>14</td>
      <td>2</td>
      <td>0</td>
      <td>2.80</td>
      <td>14.95</td>
      <td>7</td>
      <td>2</td>
      <td>0</td>
      <td>0.4</td>
      <td>4.40</td>
      <td>21</td>
      <td>10</td>
      <td>0</td>
      <td>47.62</td>
      <td>23.08</td>
      <td>8</td>
      <td>2</td>
      <td>3</td>
      <td>1</td>
      <td>0</td>
      <td>0.4</td>
      <td>10.39</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
    </tr>
    <tr>
      <th>4</th>
      <td>15</td>
      <td>황민경 (L)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>NaN</td>
      <td>2</td>
      <td>12</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>16.67</td>
      <td>7.84</td>
      <td>9</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>22.22</td>
      <td>9.78</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>10.34</td>
      <td>15</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>14.42</td>
      <td>15</td>
      <td>13</td>
      <td>2</td>
      <td>0</td>
      <td>3.25</td>
      <td>14.02</td>
      <td>6</td>
      <td>2</td>
      <td>0</td>
      <td>0.5</td>
      <td>3.77</td>
      <td>33</td>
      <td>14</td>
      <td>5</td>
      <td>27.27</td>
      <td>36.26</td>
      <td>10</td>
      <td>0</td>
      <td>3</td>
      <td>6</td>
      <td>0</td>
      <td>0.0</td>
      <td>12.99</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>5</th>
      <td>12</td>
      <td>이다현 (C)</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>O</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>100.00</td>
      <td>1.31</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>100.00</td>
      <td>1.09</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>100.0</td>
      <td>5.56</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>2</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>1.92</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0.2</td>
      <td>0.63</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>7</td>
      <td>0</td>
      <td>4</td>
      <td>2</td>
      <td>0</td>
      <td>0.0</td>
      <td>9.09</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>6</th>
      <td>3</td>
      <td>김다인 (S)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>22</td>
      <td>0</td>
      <td>3</td>
      <td>0.0</td>
      <td>21.15</td>
      <td>19</td>
      <td>15</td>
      <td>4</td>
      <td>0</td>
      <td>3.00</td>
      <td>17.76</td>
      <td>125</td>
      <td>50</td>
      <td>3</td>
      <td>10.0</td>
      <td>78.62</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>9</td>
      <td>1</td>
      <td>5</td>
      <td>3</td>
      <td>0</td>
      <td>0.2</td>
      <td>11.69</td>
      <td>1</td>
      <td>0</td>
      <td>6</td>
    </tr>
    <tr>
      <th>7</th>
      <td>8</td>
      <td>김연견 (Li)</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>10</td>
      <td>8</td>
      <td>2</td>
      <td>0</td>
      <td>1.60</td>
      <td>9.35</td>
      <td>4</td>
      <td>1</td>
      <td>0</td>
      <td>0.2</td>
      <td>2.52</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>11</td>
      <td>김주하 (Li)</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>16</td>
      <td>14</td>
      <td>2</td>
      <td>0</td>
      <td>2.80</td>
      <td>14.95</td>
      <td>4</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>2.52</td>
      <td>22</td>
      <td>10</td>
      <td>3</td>
      <td>31.82</td>
      <td>24.18</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>6</td>
      <td>이나연 (S)</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>13</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>4</td>
      <td>3</td>
      <td>0</td>
      <td>3.0</td>
      <td>2.52</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>20</td>
      <td>전하리 (L)</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>13</td>
      <td>13</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>4</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>3.85</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>4</td>
      <td>황연주 (R)</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>No.</th>
      <th>이름</th>
      <th>출전세트_1set</th>
      <th>출전세트_2set</th>
      <th>출전세트_3set</th>
      <th>출전세트_4set</th>
      <th>출전세트_5set</th>
      <th>득점_득점</th>
      <th>공격종합_시도</th>
      <th>공격종합_성공</th>
      <th>공격종합_공격차단</th>
      <th>공격종합_범실</th>
      <th>공격종합_성공률</th>
      <th>공격종합_점유율</th>
      <th>오픈_시도</th>
      <th>오픈_성공</th>
      <th>오픈_공격차단</th>
      <th>오픈_범실</th>
      <th>오픈_성공률</th>
      <th>오픈_점유율</th>
      <th>시간차_시도</th>
      <th>시간차_성공</th>
      <th>시간차_공격차단</th>
      <th>시간차_범실</th>
      <th>시간차_성공률</th>
      <th>시간차_점유율</th>
      <th>이동_시도</th>
      <th>이동_성공</th>
      <th>이동_공격차단</th>
      <th>이동_범실</th>
      <th>이동_성공률</th>
      <th>이동_점유율</th>
      <th>후위_시도</th>
      <th>후위_성공</th>
      <th>후위_공격차단</th>
      <th>후위_범실</th>
      <th>후위_성공률</th>
      <th>후위_점유율</th>
      <th>속공_시도</th>
      <th>속공_성공</th>
      <th>속공_공격차단</th>
      <th>속공_범실</th>
      <th>속공_성공률</th>
      <th>속공_점유율</th>
      <th>퀵오픈_시도</th>
      <th>퀵오픈_성공</th>
      <th>퀵오픈_공격차단</th>
      <th>퀵오픈_범실</th>
      <th>퀵오픈_성공률</th>
      <th>퀵오픈_점유율</th>
      <th>서브_시도</th>
      <th>서브_성공</th>
      <th>서브_범실</th>
      <th>서브_성공률</th>
      <th>서브_점유율</th>
      <th>디그_시도</th>
      <th>디그_성공</th>
      <th>디그_실패</th>
      <th>디그_범실</th>
      <th>디그_세트당</th>
      <th>디그_점유율</th>
      <th>세트_시도</th>
      <th>세트_성공</th>
      <th>세트_범실</th>
      <th>세트_세트당</th>
      <th>세트_점유율</th>
      <th>리시브_시도</th>
      <th>리시브_정확</th>
      <th>리시브_실패</th>
      <th>리시브_세트당</th>
      <th>리시브_점유율</th>
      <th>블로킹_시도</th>
      <th>블로킹_성공</th>
      <th>블로킹_유효블락</th>
      <th>블로킹_실패</th>
      <th>블로킹_범실</th>
      <th>블로킹_세트당</th>
      <th>블로킹_점유율</th>
      <th>블로킹_어시스트</th>
      <th>벌칙_벌칙</th>
      <th>범실_범실</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>13</td>
      <td>러츠 (R)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>33</td>
      <td>66</td>
      <td>26</td>
      <td>4</td>
      <td>6</td>
      <td>39.39</td>
      <td>42.31</td>
      <td>37</td>
      <td>12</td>
      <td>2</td>
      <td>4</td>
      <td>32.43</td>
      <td>46.25</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>14</td>
      <td>7</td>
      <td>0</td>
      <td>2</td>
      <td>50.0</td>
      <td>87.50</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>15</td>
      <td>7</td>
      <td>2</td>
      <td>0</td>
      <td>46.67</td>
      <td>30.0</td>
      <td>13</td>
      <td>2</td>
      <td>3</td>
      <td>0.40</td>
      <td>12.75</td>
      <td>9</td>
      <td>5</td>
      <td>4</td>
      <td>0</td>
      <td>1.00</td>
      <td>9.57</td>
      <td>5</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.16</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>15</td>
      <td>5</td>
      <td>3</td>
      <td>2</td>
      <td>0</td>
      <td>1.0</td>
      <td>20.83</td>
      <td>2</td>
      <td>0</td>
      <td>10</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10</td>
      <td>강소휘 (L)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>21</td>
      <td>37</td>
      <td>19</td>
      <td>2</td>
      <td>0</td>
      <td>51.35</td>
      <td>23.72</td>
      <td>15</td>
      <td>8</td>
      <td>0</td>
      <td>0</td>
      <td>53.33</td>
      <td>18.75</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>6.25</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>21</td>
      <td>11</td>
      <td>2</td>
      <td>0</td>
      <td>52.38</td>
      <td>42.0</td>
      <td>15</td>
      <td>2</td>
      <td>4</td>
      <td>0.40</td>
      <td>14.71</td>
      <td>12</td>
      <td>10</td>
      <td>2</td>
      <td>0</td>
      <td>2.00</td>
      <td>12.77</td>
      <td>5</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>3.16</td>
      <td>26</td>
      <td>14</td>
      <td>0</td>
      <td>53.85</td>
      <td>27.66</td>
      <td>8</td>
      <td>0</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>0.0</td>
      <td>11.11</td>
      <td>1</td>
      <td>0</td>
      <td>4</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>이소영 (L)</td>
      <td>O</td>
      <td>O</td>
      <td>19</td>
      <td>O</td>
      <td>O</td>
      <td>9</td>
      <td>29</td>
      <td>7</td>
      <td>4</td>
      <td>2</td>
      <td>24.14</td>
      <td>18.59</td>
      <td>17</td>
      <td>5</td>
      <td>1</td>
      <td>0</td>
      <td>29.41</td>
      <td>21.25</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>6.25</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>11</td>
      <td>2</td>
      <td>3</td>
      <td>2</td>
      <td>18.18</td>
      <td>22.0</td>
      <td>11</td>
      <td>1</td>
      <td>0</td>
      <td>0.20</td>
      <td>10.78</td>
      <td>11</td>
      <td>8</td>
      <td>3</td>
      <td>0</td>
      <td>1.60</td>
      <td>11.70</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0.2</td>
      <td>0.63</td>
      <td>26</td>
      <td>8</td>
      <td>0</td>
      <td>30.77</td>
      <td>27.66</td>
      <td>11</td>
      <td>1</td>
      <td>2</td>
      <td>3</td>
      <td>0</td>
      <td>0.2</td>
      <td>15.28</td>
      <td>1</td>
      <td>0</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>9</td>
      <td>한수지 (C)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>7</td>
      <td>5</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>40.00</td>
      <td>3.21</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>50.00</td>
      <td>2.50</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>3</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>33.33</td>
      <td>30.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>11</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>10.78</td>
      <td>7</td>
      <td>7</td>
      <td>0</td>
      <td>0</td>
      <td>1.40</td>
      <td>7.45</td>
      <td>4</td>
      <td>2</td>
      <td>0</td>
      <td>0.4</td>
      <td>2.53</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>1.06</td>
      <td>23</td>
      <td>5</td>
      <td>10</td>
      <td>6</td>
      <td>0</td>
      <td>1.0</td>
      <td>31.94</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>7</td>
      <td>안혜진 (S)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>6</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>100.00</td>
      <td>0.64</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>100.00</td>
      <td>1.25</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>26</td>
      <td>4</td>
      <td>1</td>
      <td>0.80</td>
      <td>25.49</td>
      <td>19</td>
      <td>17</td>
      <td>2</td>
      <td>0</td>
      <td>3.40</td>
      <td>20.21</td>
      <td>120</td>
      <td>44</td>
      <td>1</td>
      <td>8.8</td>
      <td>75.95</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>6</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>0.2</td>
      <td>8.33</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
    </tr>
    <tr>
      <th>5</th>
      <td>16</td>
      <td>김유리 (C)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>3</td>
      <td>8</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>37.50</td>
      <td>5.13</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>1.25</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>7</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>42.86</td>
      <td>70.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>5</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>4.90</td>
      <td>4</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>0.40</td>
      <td>4.26</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>1.27</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>6</td>
      <td>0</td>
      <td>2</td>
      <td>4</td>
      <td>0</td>
      <td>0.0</td>
      <td>8.33</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>18</td>
      <td>권민지 (L)</td>
      <td>NaN</td>
      <td>7</td>
      <td>16</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1</td>
      <td>5</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0.00</td>
      <td>3.21</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0.00</td>
      <td>3.75</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>2</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0.00</td>
      <td>4.0</td>
      <td>4</td>
      <td>1</td>
      <td>2</td>
      <td>0.50</td>
      <td>3.92</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>1.00</td>
      <td>2.13</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>0.5</td>
      <td>1.27</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0.0</td>
      <td>2.78</td>
      <td>0</td>
      <td>0</td>
      <td>3</td>
    </tr>
    <tr>
      <th>7</th>
      <td>19</td>
      <td>유서연 (R)</td>
      <td>1</td>
      <td>NaN</td>
      <td>O</td>
      <td>NaN</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>0.00</td>
      <td>2.56</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0.00</td>
      <td>3.75</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0.00</td>
      <td>2.0</td>
      <td>4</td>
      <td>1</td>
      <td>0</td>
      <td>0.33</td>
      <td>3.92</td>
      <td>3</td>
      <td>1</td>
      <td>2</td>
      <td>0</td>
      <td>0.33</td>
      <td>3.19</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>10</td>
      <td>4</td>
      <td>0</td>
      <td>40.00</td>
      <td>10.64</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0.0</td>
      <td>1.39</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
    </tr>
    <tr>
      <th>8</th>
      <td>6</td>
      <td>이원정 (S)</td>
      <td>NaN</td>
      <td>13</td>
      <td>7</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.64</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>1.25</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>1.96</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>1.00</td>
      <td>2.13</td>
      <td>8</td>
      <td>4</td>
      <td>0</td>
      <td>2.0</td>
      <td>5.06</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>8</td>
      <td>김해빈 (L)</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>9</td>
      <td>9</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>6</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>5.88</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0.00</td>
      <td>1.06</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>2</td>
      <td>이현 (S)</td>
      <td>16</td>
      <td>16</td>
      <td>9</td>
      <td>16</td>
      <td>16</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>5</td>
      <td>0</td>
      <td>1</td>
      <td>0.00</td>
      <td>4.90</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0.00</td>
      <td>1.06</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>11</th>
      <td>4</td>
      <td>한다혜 (Li)</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>18</td>
      <td>18</td>
      <td>0</td>
      <td>0</td>
      <td>3.60</td>
      <td>19.15</td>
      <td>9</td>
      <td>3</td>
      <td>0</td>
      <td>0.6</td>
      <td>5.70</td>
      <td>31</td>
      <td>13</td>
      <td>2</td>
      <td>35.48</td>
      <td>32.98</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>12</td>
      <td>한수진 (Li)</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>5</td>
      <td>4</td>
      <td>1</td>
      <td>0</td>
      <td>1.33</td>
      <td>5.32</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>1.27</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>


<br/>
<br/>
<br/>
<br/>

#### 데이터 정리
- team1, team2 동시에 처리


```python
# No. 열 삭제
team1 = team1.drop('No.', axis = 1)
team2 = team2.drop('No.', axis = 1)
team1
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>이름</th>
      <th>출전세트_1set</th>
      <th>출전세트_2set</th>
      <th>출전세트_3set</th>
      <th>출전세트_4set</th>
      <th>출전세트_5set</th>
      <th>득점_득점</th>
      <th>공격종합_시도</th>
      <th>공격종합_성공</th>
      <th>공격종합_공격차단</th>
      <th>공격종합_범실</th>
      <th>공격종합_성공률</th>
      <th>공격종합_점유율</th>
      <th>오픈_시도</th>
      <th>오픈_성공</th>
      <th>오픈_공격차단</th>
      <th>오픈_범실</th>
      <th>오픈_성공률</th>
      <th>오픈_점유율</th>
      <th>시간차_시도</th>
      <th>시간차_성공</th>
      <th>시간차_공격차단</th>
      <th>시간차_범실</th>
      <th>시간차_성공률</th>
      <th>시간차_점유율</th>
      <th>이동_시도</th>
      <th>이동_성공</th>
      <th>이동_공격차단</th>
      <th>이동_범실</th>
      <th>이동_성공률</th>
      <th>이동_점유율</th>
      <th>후위_시도</th>
      <th>후위_성공</th>
      <th>후위_공격차단</th>
      <th>후위_범실</th>
      <th>후위_성공률</th>
      <th>후위_점유율</th>
      <th>속공_시도</th>
      <th>속공_성공</th>
      <th>속공_공격차단</th>
      <th>속공_범실</th>
      <th>속공_성공률</th>
      <th>속공_점유율</th>
      <th>퀵오픈_시도</th>
      <th>퀵오픈_성공</th>
      <th>퀵오픈_공격차단</th>
      <th>퀵오픈_범실</th>
      <th>퀵오픈_성공률</th>
      <th>퀵오픈_점유율</th>
      <th>서브_시도</th>
      <th>서브_성공</th>
      <th>서브_범실</th>
      <th>서브_성공률</th>
      <th>서브_점유율</th>
      <th>디그_시도</th>
      <th>디그_성공</th>
      <th>디그_실패</th>
      <th>디그_범실</th>
      <th>디그_세트당</th>
      <th>디그_점유율</th>
      <th>세트_시도</th>
      <th>세트_성공</th>
      <th>세트_범실</th>
      <th>세트_세트당</th>
      <th>세트_점유율</th>
      <th>리시브_시도</th>
      <th>리시브_정확</th>
      <th>리시브_실패</th>
      <th>리시브_세트당</th>
      <th>리시브_점유율</th>
      <th>블로킹_시도</th>
      <th>블로킹_성공</th>
      <th>블로킹_유효블락</th>
      <th>블로킹_실패</th>
      <th>블로킹_범실</th>
      <th>블로킹_세트당</th>
      <th>블로킹_점유율</th>
      <th>블로킹_어시스트</th>
      <th>벌칙_벌칙</th>
      <th>범실_범실</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>루소 (L)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>28</td>
      <td>54</td>
      <td>22</td>
      <td>3</td>
      <td>3</td>
      <td>40.74</td>
      <td>35.29</td>
      <td>27</td>
      <td>10</td>
      <td>3</td>
      <td>2</td>
      <td>37.04</td>
      <td>29.35</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>12</td>
      <td>7</td>
      <td>0</td>
      <td>0</td>
      <td>58.33</td>
      <td>92.31</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>15</td>
      <td>5</td>
      <td>0</td>
      <td>1</td>
      <td>33.33</td>
      <td>51.72</td>
      <td>16</td>
      <td>2</td>
      <td>3</td>
      <td>0.4</td>
      <td>15.38</td>
      <td>11</td>
      <td>8</td>
      <td>3</td>
      <td>0</td>
      <td>1.60</td>
      <td>10.28</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>1.89</td>
      <td>12</td>
      <td>2</td>
      <td>3</td>
      <td>0.00</td>
      <td>13.19</td>
      <td>15</td>
      <td>4</td>
      <td>2</td>
      <td>5</td>
      <td>0</td>
      <td>0.8</td>
      <td>19.48</td>
      <td>2</td>
      <td>0</td>
      <td>6</td>
    </tr>
    <tr>
      <th>1</th>
      <td>정지윤 (C)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>21</td>
      <td>34</td>
      <td>20</td>
      <td>3</td>
      <td>1</td>
      <td>58.82</td>
      <td>22.22</td>
      <td>26</td>
      <td>16</td>
      <td>2</td>
      <td>1</td>
      <td>61.54</td>
      <td>28.26</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>5</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>40.0</td>
      <td>27.78</td>
      <td>3</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>66.67</td>
      <td>10.34</td>
      <td>9</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>8.65</td>
      <td>10</td>
      <td>8</td>
      <td>2</td>
      <td>0</td>
      <td>1.60</td>
      <td>9.35</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>0.2</td>
      <td>1.26</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>15</td>
      <td>1</td>
      <td>5</td>
      <td>4</td>
      <td>0</td>
      <td>0.2</td>
      <td>19.48</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>양효진 (C)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>18</td>
      <td>32</td>
      <td>15</td>
      <td>2</td>
      <td>2</td>
      <td>46.88</td>
      <td>20.92</td>
      <td>20</td>
      <td>9</td>
      <td>1</td>
      <td>0</td>
      <td>45.00</td>
      <td>21.74</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>12</td>
      <td>6</td>
      <td>1</td>
      <td>2</td>
      <td>50.0</td>
      <td>66.67</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>21</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>20.19</td>
      <td>10</td>
      <td>7</td>
      <td>3</td>
      <td>0</td>
      <td>1.40</td>
      <td>9.35</td>
      <td>3</td>
      <td>2</td>
      <td>0</td>
      <td>0.4</td>
      <td>1.89</td>
      <td>3</td>
      <td>1</td>
      <td>0</td>
      <td>33.33</td>
      <td>3.30</td>
      <td>13</td>
      <td>3</td>
      <td>2</td>
      <td>5</td>
      <td>0</td>
      <td>0.6</td>
      <td>16.88</td>
      <td>5</td>
      <td>0</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>고예림 (L)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>8</td>
      <td>19</td>
      <td>6</td>
      <td>3</td>
      <td>2</td>
      <td>31.58</td>
      <td>12.42</td>
      <td>9</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>22.22</td>
      <td>9.78</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>7.69</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>8</td>
      <td>3</td>
      <td>1</td>
      <td>2</td>
      <td>37.50</td>
      <td>27.59</td>
      <td>15</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>14.42</td>
      <td>16</td>
      <td>14</td>
      <td>2</td>
      <td>0</td>
      <td>2.80</td>
      <td>14.95</td>
      <td>7</td>
      <td>2</td>
      <td>0</td>
      <td>0.4</td>
      <td>4.40</td>
      <td>21</td>
      <td>10</td>
      <td>0</td>
      <td>47.62</td>
      <td>23.08</td>
      <td>8</td>
      <td>2</td>
      <td>3</td>
      <td>1</td>
      <td>0</td>
      <td>0.4</td>
      <td>10.39</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
    </tr>
    <tr>
      <th>4</th>
      <td>황민경 (L)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>NaN</td>
      <td>2</td>
      <td>12</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>16.67</td>
      <td>7.84</td>
      <td>9</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>22.22</td>
      <td>9.78</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>10.34</td>
      <td>15</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>14.42</td>
      <td>15</td>
      <td>13</td>
      <td>2</td>
      <td>0</td>
      <td>3.25</td>
      <td>14.02</td>
      <td>6</td>
      <td>2</td>
      <td>0</td>
      <td>0.5</td>
      <td>3.77</td>
      <td>33</td>
      <td>14</td>
      <td>5</td>
      <td>27.27</td>
      <td>36.26</td>
      <td>10</td>
      <td>0</td>
      <td>3</td>
      <td>6</td>
      <td>0</td>
      <td>0.0</td>
      <td>12.99</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>5</th>
      <td>이다현 (C)</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>O</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>100.00</td>
      <td>1.31</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>100.00</td>
      <td>1.09</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>100.0</td>
      <td>5.56</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>2</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>1.92</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0.2</td>
      <td>0.63</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>7</td>
      <td>0</td>
      <td>4</td>
      <td>2</td>
      <td>0</td>
      <td>0.0</td>
      <td>9.09</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>6</th>
      <td>김다인 (S)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>22</td>
      <td>0</td>
      <td>3</td>
      <td>0.0</td>
      <td>21.15</td>
      <td>19</td>
      <td>15</td>
      <td>4</td>
      <td>0</td>
      <td>3.00</td>
      <td>17.76</td>
      <td>125</td>
      <td>50</td>
      <td>3</td>
      <td>10.0</td>
      <td>78.62</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>9</td>
      <td>1</td>
      <td>5</td>
      <td>3</td>
      <td>0</td>
      <td>0.2</td>
      <td>11.69</td>
      <td>1</td>
      <td>0</td>
      <td>6</td>
    </tr>
    <tr>
      <th>7</th>
      <td>김연견 (Li)</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>10</td>
      <td>8</td>
      <td>2</td>
      <td>0</td>
      <td>1.60</td>
      <td>9.35</td>
      <td>4</td>
      <td>1</td>
      <td>0</td>
      <td>0.2</td>
      <td>2.52</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>김주하 (Li)</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>16</td>
      <td>14</td>
      <td>2</td>
      <td>0</td>
      <td>2.80</td>
      <td>14.95</td>
      <td>4</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>2.52</td>
      <td>22</td>
      <td>10</td>
      <td>3</td>
      <td>31.82</td>
      <td>24.18</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>이나연 (S)</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>13</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>4</td>
      <td>3</td>
      <td>0</td>
      <td>3.0</td>
      <td>2.52</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>전하리 (L)</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>13</td>
      <td>13</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>4</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>3.85</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>황연주 (R)</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 3경기 이상 출전한 선수만 유지 (출전 세트 결측치가 3개 이상인 선수 제거)
subset = ['출전세트_1set', '출전세트_2set', '출전세트_3set', '출전세트_4set', '출전세트_5set']

# thresh = 3 : 정상 값(Not NaN)이 3개 이상만 유지
team1 = team1.dropna(subset = subset, thresh = 3)
team2 = team2.dropna(subset = subset, thresh = 3)
team1
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>이름</th>
      <th>출전세트_1set</th>
      <th>출전세트_2set</th>
      <th>출전세트_3set</th>
      <th>출전세트_4set</th>
      <th>출전세트_5set</th>
      <th>득점_득점</th>
      <th>공격종합_시도</th>
      <th>공격종합_성공</th>
      <th>공격종합_공격차단</th>
      <th>공격종합_범실</th>
      <th>공격종합_성공률</th>
      <th>공격종합_점유율</th>
      <th>오픈_시도</th>
      <th>오픈_성공</th>
      <th>오픈_공격차단</th>
      <th>오픈_범실</th>
      <th>오픈_성공률</th>
      <th>오픈_점유율</th>
      <th>시간차_시도</th>
      <th>시간차_성공</th>
      <th>시간차_공격차단</th>
      <th>시간차_범실</th>
      <th>시간차_성공률</th>
      <th>시간차_점유율</th>
      <th>이동_시도</th>
      <th>이동_성공</th>
      <th>이동_공격차단</th>
      <th>이동_범실</th>
      <th>이동_성공률</th>
      <th>이동_점유율</th>
      <th>후위_시도</th>
      <th>후위_성공</th>
      <th>후위_공격차단</th>
      <th>후위_범실</th>
      <th>후위_성공률</th>
      <th>후위_점유율</th>
      <th>속공_시도</th>
      <th>속공_성공</th>
      <th>속공_공격차단</th>
      <th>속공_범실</th>
      <th>속공_성공률</th>
      <th>속공_점유율</th>
      <th>퀵오픈_시도</th>
      <th>퀵오픈_성공</th>
      <th>퀵오픈_공격차단</th>
      <th>퀵오픈_범실</th>
      <th>퀵오픈_성공률</th>
      <th>퀵오픈_점유율</th>
      <th>서브_시도</th>
      <th>서브_성공</th>
      <th>서브_범실</th>
      <th>서브_성공률</th>
      <th>서브_점유율</th>
      <th>디그_시도</th>
      <th>디그_성공</th>
      <th>디그_실패</th>
      <th>디그_범실</th>
      <th>디그_세트당</th>
      <th>디그_점유율</th>
      <th>세트_시도</th>
      <th>세트_성공</th>
      <th>세트_범실</th>
      <th>세트_세트당</th>
      <th>세트_점유율</th>
      <th>리시브_시도</th>
      <th>리시브_정확</th>
      <th>리시브_실패</th>
      <th>리시브_세트당</th>
      <th>리시브_점유율</th>
      <th>블로킹_시도</th>
      <th>블로킹_성공</th>
      <th>블로킹_유효블락</th>
      <th>블로킹_실패</th>
      <th>블로킹_범실</th>
      <th>블로킹_세트당</th>
      <th>블로킹_점유율</th>
      <th>블로킹_어시스트</th>
      <th>벌칙_벌칙</th>
      <th>범실_범실</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>루소 (L)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>28</td>
      <td>54</td>
      <td>22</td>
      <td>3</td>
      <td>3</td>
      <td>40.74</td>
      <td>35.29</td>
      <td>27</td>
      <td>10</td>
      <td>3</td>
      <td>2</td>
      <td>37.04</td>
      <td>29.35</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>12</td>
      <td>7</td>
      <td>0</td>
      <td>0</td>
      <td>58.33</td>
      <td>92.31</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>15</td>
      <td>5</td>
      <td>0</td>
      <td>1</td>
      <td>33.33</td>
      <td>51.72</td>
      <td>16</td>
      <td>2</td>
      <td>3</td>
      <td>0.4</td>
      <td>15.38</td>
      <td>11</td>
      <td>8</td>
      <td>3</td>
      <td>0</td>
      <td>1.60</td>
      <td>10.28</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>1.89</td>
      <td>12</td>
      <td>2</td>
      <td>3</td>
      <td>0.00</td>
      <td>13.19</td>
      <td>15</td>
      <td>4</td>
      <td>2</td>
      <td>5</td>
      <td>0</td>
      <td>0.8</td>
      <td>19.48</td>
      <td>2</td>
      <td>0</td>
      <td>6</td>
    </tr>
    <tr>
      <th>1</th>
      <td>정지윤 (C)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>21</td>
      <td>34</td>
      <td>20</td>
      <td>3</td>
      <td>1</td>
      <td>58.82</td>
      <td>22.22</td>
      <td>26</td>
      <td>16</td>
      <td>2</td>
      <td>1</td>
      <td>61.54</td>
      <td>28.26</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>5</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>40.0</td>
      <td>27.78</td>
      <td>3</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>66.67</td>
      <td>10.34</td>
      <td>9</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>8.65</td>
      <td>10</td>
      <td>8</td>
      <td>2</td>
      <td>0</td>
      <td>1.60</td>
      <td>9.35</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>0.2</td>
      <td>1.26</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>15</td>
      <td>1</td>
      <td>5</td>
      <td>4</td>
      <td>0</td>
      <td>0.2</td>
      <td>19.48</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>양효진 (C)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>18</td>
      <td>32</td>
      <td>15</td>
      <td>2</td>
      <td>2</td>
      <td>46.88</td>
      <td>20.92</td>
      <td>20</td>
      <td>9</td>
      <td>1</td>
      <td>0</td>
      <td>45.00</td>
      <td>21.74</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>12</td>
      <td>6</td>
      <td>1</td>
      <td>2</td>
      <td>50.0</td>
      <td>66.67</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>21</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>20.19</td>
      <td>10</td>
      <td>7</td>
      <td>3</td>
      <td>0</td>
      <td>1.40</td>
      <td>9.35</td>
      <td>3</td>
      <td>2</td>
      <td>0</td>
      <td>0.4</td>
      <td>1.89</td>
      <td>3</td>
      <td>1</td>
      <td>0</td>
      <td>33.33</td>
      <td>3.30</td>
      <td>13</td>
      <td>3</td>
      <td>2</td>
      <td>5</td>
      <td>0</td>
      <td>0.6</td>
      <td>16.88</td>
      <td>5</td>
      <td>0</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>고예림 (L)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>8</td>
      <td>19</td>
      <td>6</td>
      <td>3</td>
      <td>2</td>
      <td>31.58</td>
      <td>12.42</td>
      <td>9</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>22.22</td>
      <td>9.78</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>7.69</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>8</td>
      <td>3</td>
      <td>1</td>
      <td>2</td>
      <td>37.50</td>
      <td>27.59</td>
      <td>15</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>14.42</td>
      <td>16</td>
      <td>14</td>
      <td>2</td>
      <td>0</td>
      <td>2.80</td>
      <td>14.95</td>
      <td>7</td>
      <td>2</td>
      <td>0</td>
      <td>0.4</td>
      <td>4.40</td>
      <td>21</td>
      <td>10</td>
      <td>0</td>
      <td>47.62</td>
      <td>23.08</td>
      <td>8</td>
      <td>2</td>
      <td>3</td>
      <td>1</td>
      <td>0</td>
      <td>0.4</td>
      <td>10.39</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
    </tr>
    <tr>
      <th>4</th>
      <td>황민경 (L)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>NaN</td>
      <td>2</td>
      <td>12</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>16.67</td>
      <td>7.84</td>
      <td>9</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>22.22</td>
      <td>9.78</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>10.34</td>
      <td>15</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>14.42</td>
      <td>15</td>
      <td>13</td>
      <td>2</td>
      <td>0</td>
      <td>3.25</td>
      <td>14.02</td>
      <td>6</td>
      <td>2</td>
      <td>0</td>
      <td>0.5</td>
      <td>3.77</td>
      <td>33</td>
      <td>14</td>
      <td>5</td>
      <td>27.27</td>
      <td>36.26</td>
      <td>10</td>
      <td>0</td>
      <td>3</td>
      <td>6</td>
      <td>0</td>
      <td>0.0</td>
      <td>12.99</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>5</th>
      <td>이다현 (C)</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>O</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>100.00</td>
      <td>1.31</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>100.00</td>
      <td>1.09</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>100.0</td>
      <td>5.56</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>2</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>1.92</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0.2</td>
      <td>0.63</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>7</td>
      <td>0</td>
      <td>4</td>
      <td>2</td>
      <td>0</td>
      <td>0.0</td>
      <td>9.09</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>6</th>
      <td>김다인 (S)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>22</td>
      <td>0</td>
      <td>3</td>
      <td>0.0</td>
      <td>21.15</td>
      <td>19</td>
      <td>15</td>
      <td>4</td>
      <td>0</td>
      <td>3.00</td>
      <td>17.76</td>
      <td>125</td>
      <td>50</td>
      <td>3</td>
      <td>10.0</td>
      <td>78.62</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>9</td>
      <td>1</td>
      <td>5</td>
      <td>3</td>
      <td>0</td>
      <td>0.2</td>
      <td>11.69</td>
      <td>1</td>
      <td>0</td>
      <td>6</td>
    </tr>
    <tr>
      <th>7</th>
      <td>김연견 (Li)</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>10</td>
      <td>8</td>
      <td>2</td>
      <td>0</td>
      <td>1.60</td>
      <td>9.35</td>
      <td>4</td>
      <td>1</td>
      <td>0</td>
      <td>0.2</td>
      <td>2.52</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>김주하 (Li)</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>16</td>
      <td>14</td>
      <td>2</td>
      <td>0</td>
      <td>2.80</td>
      <td>14.95</td>
      <td>4</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>2.52</td>
      <td>22</td>
      <td>10</td>
      <td>3</td>
      <td>31.82</td>
      <td>24.18</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 스타팅멤버 표시 (3세트 이상 스타팅멤버라면 1 아니면 0)
team1['스타팅멤버'] = 0
team2['스타팅멤버'] = 0

team1.loc[(team1.iloc[:, 1:6] == 'O').sum(axis = 1) >= 3, '스타팅멤버'] = 1
team2.loc[(team2.iloc[:, 1:6] == 'O').sum(axis = 1) >= 3, '스타팅멤버'] = 1
team1
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>이름</th>
      <th>출전세트_1set</th>
      <th>출전세트_2set</th>
      <th>출전세트_3set</th>
      <th>출전세트_4set</th>
      <th>출전세트_5set</th>
      <th>득점_득점</th>
      <th>공격종합_시도</th>
      <th>공격종합_성공</th>
      <th>공격종합_공격차단</th>
      <th>공격종합_범실</th>
      <th>공격종합_성공률</th>
      <th>공격종합_점유율</th>
      <th>오픈_시도</th>
      <th>오픈_성공</th>
      <th>오픈_공격차단</th>
      <th>오픈_범실</th>
      <th>오픈_성공률</th>
      <th>오픈_점유율</th>
      <th>시간차_시도</th>
      <th>시간차_성공</th>
      <th>시간차_공격차단</th>
      <th>시간차_범실</th>
      <th>시간차_성공률</th>
      <th>시간차_점유율</th>
      <th>이동_시도</th>
      <th>이동_성공</th>
      <th>이동_공격차단</th>
      <th>이동_범실</th>
      <th>이동_성공률</th>
      <th>이동_점유율</th>
      <th>후위_시도</th>
      <th>후위_성공</th>
      <th>후위_공격차단</th>
      <th>후위_범실</th>
      <th>후위_성공률</th>
      <th>후위_점유율</th>
      <th>속공_시도</th>
      <th>속공_성공</th>
      <th>속공_공격차단</th>
      <th>속공_범실</th>
      <th>속공_성공률</th>
      <th>속공_점유율</th>
      <th>퀵오픈_시도</th>
      <th>퀵오픈_성공</th>
      <th>퀵오픈_공격차단</th>
      <th>퀵오픈_범실</th>
      <th>퀵오픈_성공률</th>
      <th>퀵오픈_점유율</th>
      <th>서브_시도</th>
      <th>서브_성공</th>
      <th>서브_범실</th>
      <th>서브_성공률</th>
      <th>서브_점유율</th>
      <th>디그_시도</th>
      <th>디그_성공</th>
      <th>디그_실패</th>
      <th>디그_범실</th>
      <th>디그_세트당</th>
      <th>디그_점유율</th>
      <th>세트_시도</th>
      <th>세트_성공</th>
      <th>세트_범실</th>
      <th>세트_세트당</th>
      <th>세트_점유율</th>
      <th>리시브_시도</th>
      <th>리시브_정확</th>
      <th>리시브_실패</th>
      <th>리시브_세트당</th>
      <th>리시브_점유율</th>
      <th>블로킹_시도</th>
      <th>블로킹_성공</th>
      <th>블로킹_유효블락</th>
      <th>블로킹_실패</th>
      <th>블로킹_범실</th>
      <th>블로킹_세트당</th>
      <th>블로킹_점유율</th>
      <th>블로킹_어시스트</th>
      <th>벌칙_벌칙</th>
      <th>범실_범실</th>
      <th>스타팅멤버</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>루소 (L)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>28</td>
      <td>54</td>
      <td>22</td>
      <td>3</td>
      <td>3</td>
      <td>40.74</td>
      <td>35.29</td>
      <td>27</td>
      <td>10</td>
      <td>3</td>
      <td>2</td>
      <td>37.04</td>
      <td>29.35</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>12</td>
      <td>7</td>
      <td>0</td>
      <td>0</td>
      <td>58.33</td>
      <td>92.31</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>15</td>
      <td>5</td>
      <td>0</td>
      <td>1</td>
      <td>33.33</td>
      <td>51.72</td>
      <td>16</td>
      <td>2</td>
      <td>3</td>
      <td>0.4</td>
      <td>15.38</td>
      <td>11</td>
      <td>8</td>
      <td>3</td>
      <td>0</td>
      <td>1.60</td>
      <td>10.28</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>1.89</td>
      <td>12</td>
      <td>2</td>
      <td>3</td>
      <td>0.00</td>
      <td>13.19</td>
      <td>15</td>
      <td>4</td>
      <td>2</td>
      <td>5</td>
      <td>0</td>
      <td>0.8</td>
      <td>19.48</td>
      <td>2</td>
      <td>0</td>
      <td>6</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>정지윤 (C)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>21</td>
      <td>34</td>
      <td>20</td>
      <td>3</td>
      <td>1</td>
      <td>58.82</td>
      <td>22.22</td>
      <td>26</td>
      <td>16</td>
      <td>2</td>
      <td>1</td>
      <td>61.54</td>
      <td>28.26</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>5</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>40.0</td>
      <td>27.78</td>
      <td>3</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>66.67</td>
      <td>10.34</td>
      <td>9</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>8.65</td>
      <td>10</td>
      <td>8</td>
      <td>2</td>
      <td>0</td>
      <td>1.60</td>
      <td>9.35</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>0.2</td>
      <td>1.26</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>15</td>
      <td>1</td>
      <td>5</td>
      <td>4</td>
      <td>0</td>
      <td>0.2</td>
      <td>19.48</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>양효진 (C)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>18</td>
      <td>32</td>
      <td>15</td>
      <td>2</td>
      <td>2</td>
      <td>46.88</td>
      <td>20.92</td>
      <td>20</td>
      <td>9</td>
      <td>1</td>
      <td>0</td>
      <td>45.00</td>
      <td>21.74</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>12</td>
      <td>6</td>
      <td>1</td>
      <td>2</td>
      <td>50.0</td>
      <td>66.67</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>21</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>20.19</td>
      <td>10</td>
      <td>7</td>
      <td>3</td>
      <td>0</td>
      <td>1.40</td>
      <td>9.35</td>
      <td>3</td>
      <td>2</td>
      <td>0</td>
      <td>0.4</td>
      <td>1.89</td>
      <td>3</td>
      <td>1</td>
      <td>0</td>
      <td>33.33</td>
      <td>3.30</td>
      <td>13</td>
      <td>3</td>
      <td>2</td>
      <td>5</td>
      <td>0</td>
      <td>0.6</td>
      <td>16.88</td>
      <td>5</td>
      <td>0</td>
      <td>3</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>고예림 (L)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>8</td>
      <td>19</td>
      <td>6</td>
      <td>3</td>
      <td>2</td>
      <td>31.58</td>
      <td>12.42</td>
      <td>9</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>22.22</td>
      <td>9.78</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>7.69</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>8</td>
      <td>3</td>
      <td>1</td>
      <td>2</td>
      <td>37.50</td>
      <td>27.59</td>
      <td>15</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>14.42</td>
      <td>16</td>
      <td>14</td>
      <td>2</td>
      <td>0</td>
      <td>2.80</td>
      <td>14.95</td>
      <td>7</td>
      <td>2</td>
      <td>0</td>
      <td>0.4</td>
      <td>4.40</td>
      <td>21</td>
      <td>10</td>
      <td>0</td>
      <td>47.62</td>
      <td>23.08</td>
      <td>8</td>
      <td>2</td>
      <td>3</td>
      <td>1</td>
      <td>0</td>
      <td>0.4</td>
      <td>10.39</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>황민경 (L)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>NaN</td>
      <td>2</td>
      <td>12</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>16.67</td>
      <td>7.84</td>
      <td>9</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>22.22</td>
      <td>9.78</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>10.34</td>
      <td>15</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>14.42</td>
      <td>15</td>
      <td>13</td>
      <td>2</td>
      <td>0</td>
      <td>3.25</td>
      <td>14.02</td>
      <td>6</td>
      <td>2</td>
      <td>0</td>
      <td>0.5</td>
      <td>3.77</td>
      <td>33</td>
      <td>14</td>
      <td>5</td>
      <td>27.27</td>
      <td>36.26</td>
      <td>10</td>
      <td>0</td>
      <td>3</td>
      <td>6</td>
      <td>0</td>
      <td>0.0</td>
      <td>12.99</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>5</th>
      <td>이다현 (C)</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>O</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>100.00</td>
      <td>1.31</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>100.00</td>
      <td>1.09</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>100.0</td>
      <td>5.56</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>2</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>1.92</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0.2</td>
      <td>0.63</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>7</td>
      <td>0</td>
      <td>4</td>
      <td>2</td>
      <td>0</td>
      <td>0.0</td>
      <td>9.09</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>김다인 (S)</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>O</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>22</td>
      <td>0</td>
      <td>3</td>
      <td>0.0</td>
      <td>21.15</td>
      <td>19</td>
      <td>15</td>
      <td>4</td>
      <td>0</td>
      <td>3.00</td>
      <td>17.76</td>
      <td>125</td>
      <td>50</td>
      <td>3</td>
      <td>10.0</td>
      <td>78.62</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>9</td>
      <td>1</td>
      <td>5</td>
      <td>3</td>
      <td>0</td>
      <td>0.2</td>
      <td>11.69</td>
      <td>1</td>
      <td>0</td>
      <td>6</td>
      <td>1</td>
    </tr>
    <tr>
      <th>7</th>
      <td>김연견 (Li)</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>10</td>
      <td>8</td>
      <td>2</td>
      <td>0</td>
      <td>1.60</td>
      <td>9.35</td>
      <td>4</td>
      <td>1</td>
      <td>0</td>
      <td>0.2</td>
      <td>2.52</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>김주하 (Li)</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>LI</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>16</td>
      <td>14</td>
      <td>2</td>
      <td>0</td>
      <td>2.80</td>
      <td>14.95</td>
      <td>4</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>2.52</td>
      <td>22</td>
      <td>10</td>
      <td>3</td>
      <td>31.82</td>
      <td>24.18</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 출전 세트 열 삭제
subset = ['출전세트_1set', '출전세트_2set', '출전세트_3set', '출전세트_4set', '출전세트_5set']

team1 = team1.drop(subset, axis = 1)
team2 = team2.drop(subset, axis = 1)
team1
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>이름</th>
      <th>득점_득점</th>
      <th>공격종합_시도</th>
      <th>공격종합_성공</th>
      <th>공격종합_공격차단</th>
      <th>공격종합_범실</th>
      <th>공격종합_성공률</th>
      <th>공격종합_점유율</th>
      <th>오픈_시도</th>
      <th>오픈_성공</th>
      <th>오픈_공격차단</th>
      <th>오픈_범실</th>
      <th>오픈_성공률</th>
      <th>오픈_점유율</th>
      <th>시간차_시도</th>
      <th>시간차_성공</th>
      <th>시간차_공격차단</th>
      <th>시간차_범실</th>
      <th>시간차_성공률</th>
      <th>시간차_점유율</th>
      <th>이동_시도</th>
      <th>이동_성공</th>
      <th>이동_공격차단</th>
      <th>이동_범실</th>
      <th>이동_성공률</th>
      <th>이동_점유율</th>
      <th>후위_시도</th>
      <th>후위_성공</th>
      <th>후위_공격차단</th>
      <th>후위_범실</th>
      <th>후위_성공률</th>
      <th>후위_점유율</th>
      <th>속공_시도</th>
      <th>속공_성공</th>
      <th>속공_공격차단</th>
      <th>속공_범실</th>
      <th>속공_성공률</th>
      <th>속공_점유율</th>
      <th>퀵오픈_시도</th>
      <th>퀵오픈_성공</th>
      <th>퀵오픈_공격차단</th>
      <th>퀵오픈_범실</th>
      <th>퀵오픈_성공률</th>
      <th>퀵오픈_점유율</th>
      <th>서브_시도</th>
      <th>서브_성공</th>
      <th>서브_범실</th>
      <th>서브_성공률</th>
      <th>서브_점유율</th>
      <th>디그_시도</th>
      <th>디그_성공</th>
      <th>디그_실패</th>
      <th>디그_범실</th>
      <th>디그_세트당</th>
      <th>디그_점유율</th>
      <th>세트_시도</th>
      <th>세트_성공</th>
      <th>세트_범실</th>
      <th>세트_세트당</th>
      <th>세트_점유율</th>
      <th>리시브_시도</th>
      <th>리시브_정확</th>
      <th>리시브_실패</th>
      <th>리시브_세트당</th>
      <th>리시브_점유율</th>
      <th>블로킹_시도</th>
      <th>블로킹_성공</th>
      <th>블로킹_유효블락</th>
      <th>블로킹_실패</th>
      <th>블로킹_범실</th>
      <th>블로킹_세트당</th>
      <th>블로킹_점유율</th>
      <th>블로킹_어시스트</th>
      <th>벌칙_벌칙</th>
      <th>범실_범실</th>
      <th>스타팅멤버</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>루소 (L)</td>
      <td>28</td>
      <td>54</td>
      <td>22</td>
      <td>3</td>
      <td>3</td>
      <td>40.74</td>
      <td>35.29</td>
      <td>27</td>
      <td>10</td>
      <td>3</td>
      <td>2</td>
      <td>37.04</td>
      <td>29.35</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>12</td>
      <td>7</td>
      <td>0</td>
      <td>0</td>
      <td>58.33</td>
      <td>92.31</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>15</td>
      <td>5</td>
      <td>0</td>
      <td>1</td>
      <td>33.33</td>
      <td>51.72</td>
      <td>16</td>
      <td>2</td>
      <td>3</td>
      <td>0.4</td>
      <td>15.38</td>
      <td>11</td>
      <td>8</td>
      <td>3</td>
      <td>0</td>
      <td>1.60</td>
      <td>10.28</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>1.89</td>
      <td>12</td>
      <td>2</td>
      <td>3</td>
      <td>0.00</td>
      <td>13.19</td>
      <td>15</td>
      <td>4</td>
      <td>2</td>
      <td>5</td>
      <td>0</td>
      <td>0.8</td>
      <td>19.48</td>
      <td>2</td>
      <td>0</td>
      <td>6</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>정지윤 (C)</td>
      <td>21</td>
      <td>34</td>
      <td>20</td>
      <td>3</td>
      <td>1</td>
      <td>58.82</td>
      <td>22.22</td>
      <td>26</td>
      <td>16</td>
      <td>2</td>
      <td>1</td>
      <td>61.54</td>
      <td>28.26</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>5</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>40.0</td>
      <td>27.78</td>
      <td>3</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>66.67</td>
      <td>10.34</td>
      <td>9</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>8.65</td>
      <td>10</td>
      <td>8</td>
      <td>2</td>
      <td>0</td>
      <td>1.60</td>
      <td>9.35</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>0.2</td>
      <td>1.26</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>15</td>
      <td>1</td>
      <td>5</td>
      <td>4</td>
      <td>0</td>
      <td>0.2</td>
      <td>19.48</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>양효진 (C)</td>
      <td>18</td>
      <td>32</td>
      <td>15</td>
      <td>2</td>
      <td>2</td>
      <td>46.88</td>
      <td>20.92</td>
      <td>20</td>
      <td>9</td>
      <td>1</td>
      <td>0</td>
      <td>45.00</td>
      <td>21.74</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>12</td>
      <td>6</td>
      <td>1</td>
      <td>2</td>
      <td>50.0</td>
      <td>66.67</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>21</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>20.19</td>
      <td>10</td>
      <td>7</td>
      <td>3</td>
      <td>0</td>
      <td>1.40</td>
      <td>9.35</td>
      <td>3</td>
      <td>2</td>
      <td>0</td>
      <td>0.4</td>
      <td>1.89</td>
      <td>3</td>
      <td>1</td>
      <td>0</td>
      <td>33.33</td>
      <td>3.30</td>
      <td>13</td>
      <td>3</td>
      <td>2</td>
      <td>5</td>
      <td>0</td>
      <td>0.6</td>
      <td>16.88</td>
      <td>5</td>
      <td>0</td>
      <td>3</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>고예림 (L)</td>
      <td>8</td>
      <td>19</td>
      <td>6</td>
      <td>3</td>
      <td>2</td>
      <td>31.58</td>
      <td>12.42</td>
      <td>9</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>22.22</td>
      <td>9.78</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>7.69</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>8</td>
      <td>3</td>
      <td>1</td>
      <td>2</td>
      <td>37.50</td>
      <td>27.59</td>
      <td>15</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>14.42</td>
      <td>16</td>
      <td>14</td>
      <td>2</td>
      <td>0</td>
      <td>2.80</td>
      <td>14.95</td>
      <td>7</td>
      <td>2</td>
      <td>0</td>
      <td>0.4</td>
      <td>4.40</td>
      <td>21</td>
      <td>10</td>
      <td>0</td>
      <td>47.62</td>
      <td>23.08</td>
      <td>8</td>
      <td>2</td>
      <td>3</td>
      <td>1</td>
      <td>0</td>
      <td>0.4</td>
      <td>10.39</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>황민경 (L)</td>
      <td>2</td>
      <td>12</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>16.67</td>
      <td>7.84</td>
      <td>9</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>22.22</td>
      <td>9.78</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>10.34</td>
      <td>15</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>14.42</td>
      <td>15</td>
      <td>13</td>
      <td>2</td>
      <td>0</td>
      <td>3.25</td>
      <td>14.02</td>
      <td>6</td>
      <td>2</td>
      <td>0</td>
      <td>0.5</td>
      <td>3.77</td>
      <td>33</td>
      <td>14</td>
      <td>5</td>
      <td>27.27</td>
      <td>36.26</td>
      <td>10</td>
      <td>0</td>
      <td>3</td>
      <td>6</td>
      <td>0</td>
      <td>0.0</td>
      <td>12.99</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>5</th>
      <td>이다현 (C)</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>100.00</td>
      <td>1.31</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>100.00</td>
      <td>1.09</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>100.0</td>
      <td>5.56</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>2</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>1.92</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0.2</td>
      <td>0.63</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>7</td>
      <td>0</td>
      <td>4</td>
      <td>2</td>
      <td>0</td>
      <td>0.0</td>
      <td>9.09</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>김다인 (S)</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>22</td>
      <td>0</td>
      <td>3</td>
      <td>0.0</td>
      <td>21.15</td>
      <td>19</td>
      <td>15</td>
      <td>4</td>
      <td>0</td>
      <td>3.00</td>
      <td>17.76</td>
      <td>125</td>
      <td>50</td>
      <td>3</td>
      <td>10.0</td>
      <td>78.62</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>9</td>
      <td>1</td>
      <td>5</td>
      <td>3</td>
      <td>0</td>
      <td>0.2</td>
      <td>11.69</td>
      <td>1</td>
      <td>0</td>
      <td>6</td>
      <td>1</td>
    </tr>
    <tr>
      <th>7</th>
      <td>김연견 (Li)</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>10</td>
      <td>8</td>
      <td>2</td>
      <td>0</td>
      <td>1.60</td>
      <td>9.35</td>
      <td>4</td>
      <td>1</td>
      <td>0</td>
      <td>0.2</td>
      <td>2.52</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>김주하 (Li)</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>16</td>
      <td>14</td>
      <td>2</td>
      <td>0</td>
      <td>2.80</td>
      <td>14.95</td>
      <td>4</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>2.52</td>
      <td>22</td>
      <td>10</td>
      <td>3</td>
      <td>31.82</td>
      <td>24.18</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 이름과 포지션 분리하기
# "이름" 열 : 이름 정보
# "포지션" 열 : 포지션 정보

team1['포지션'] = team1['이름'].str.split().str[1].str.strip('()')
team1['이름'] = team1['이름'].str.split().str[0]

team2['포지션'] = team2['이름'].str.split().str[1].str.strip('()')
team2['이름'] = team2['이름'].str.split().str[0]
team1
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>이름</th>
      <th>득점_득점</th>
      <th>공격종합_시도</th>
      <th>공격종합_성공</th>
      <th>공격종합_공격차단</th>
      <th>공격종합_범실</th>
      <th>공격종합_성공률</th>
      <th>공격종합_점유율</th>
      <th>오픈_시도</th>
      <th>오픈_성공</th>
      <th>오픈_공격차단</th>
      <th>오픈_범실</th>
      <th>오픈_성공률</th>
      <th>오픈_점유율</th>
      <th>시간차_시도</th>
      <th>시간차_성공</th>
      <th>시간차_공격차단</th>
      <th>시간차_범실</th>
      <th>시간차_성공률</th>
      <th>시간차_점유율</th>
      <th>이동_시도</th>
      <th>이동_성공</th>
      <th>이동_공격차단</th>
      <th>이동_범실</th>
      <th>이동_성공률</th>
      <th>이동_점유율</th>
      <th>후위_시도</th>
      <th>후위_성공</th>
      <th>후위_공격차단</th>
      <th>후위_범실</th>
      <th>후위_성공률</th>
      <th>후위_점유율</th>
      <th>속공_시도</th>
      <th>속공_성공</th>
      <th>속공_공격차단</th>
      <th>속공_범실</th>
      <th>속공_성공률</th>
      <th>속공_점유율</th>
      <th>퀵오픈_시도</th>
      <th>퀵오픈_성공</th>
      <th>퀵오픈_공격차단</th>
      <th>퀵오픈_범실</th>
      <th>퀵오픈_성공률</th>
      <th>퀵오픈_점유율</th>
      <th>서브_시도</th>
      <th>서브_성공</th>
      <th>서브_범실</th>
      <th>서브_성공률</th>
      <th>서브_점유율</th>
      <th>디그_시도</th>
      <th>디그_성공</th>
      <th>디그_실패</th>
      <th>디그_범실</th>
      <th>디그_세트당</th>
      <th>디그_점유율</th>
      <th>세트_시도</th>
      <th>세트_성공</th>
      <th>세트_범실</th>
      <th>세트_세트당</th>
      <th>세트_점유율</th>
      <th>리시브_시도</th>
      <th>리시브_정확</th>
      <th>리시브_실패</th>
      <th>리시브_세트당</th>
      <th>리시브_점유율</th>
      <th>블로킹_시도</th>
      <th>블로킹_성공</th>
      <th>블로킹_유효블락</th>
      <th>블로킹_실패</th>
      <th>블로킹_범실</th>
      <th>블로킹_세트당</th>
      <th>블로킹_점유율</th>
      <th>블로킹_어시스트</th>
      <th>벌칙_벌칙</th>
      <th>범실_범실</th>
      <th>스타팅멤버</th>
      <th>포지션</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>루소</td>
      <td>28</td>
      <td>54</td>
      <td>22</td>
      <td>3</td>
      <td>3</td>
      <td>40.74</td>
      <td>35.29</td>
      <td>27</td>
      <td>10</td>
      <td>3</td>
      <td>2</td>
      <td>37.04</td>
      <td>29.35</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>12</td>
      <td>7</td>
      <td>0</td>
      <td>0</td>
      <td>58.33</td>
      <td>92.31</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>15</td>
      <td>5</td>
      <td>0</td>
      <td>1</td>
      <td>33.33</td>
      <td>51.72</td>
      <td>16</td>
      <td>2</td>
      <td>3</td>
      <td>0.4</td>
      <td>15.38</td>
      <td>11</td>
      <td>8</td>
      <td>3</td>
      <td>0</td>
      <td>1.60</td>
      <td>10.28</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>1.89</td>
      <td>12</td>
      <td>2</td>
      <td>3</td>
      <td>0.00</td>
      <td>13.19</td>
      <td>15</td>
      <td>4</td>
      <td>2</td>
      <td>5</td>
      <td>0</td>
      <td>0.8</td>
      <td>19.48</td>
      <td>2</td>
      <td>0</td>
      <td>6</td>
      <td>1</td>
      <td>L</td>
    </tr>
    <tr>
      <th>1</th>
      <td>정지윤</td>
      <td>21</td>
      <td>34</td>
      <td>20</td>
      <td>3</td>
      <td>1</td>
      <td>58.82</td>
      <td>22.22</td>
      <td>26</td>
      <td>16</td>
      <td>2</td>
      <td>1</td>
      <td>61.54</td>
      <td>28.26</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>5</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>40.0</td>
      <td>27.78</td>
      <td>3</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>66.67</td>
      <td>10.34</td>
      <td>9</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>8.65</td>
      <td>10</td>
      <td>8</td>
      <td>2</td>
      <td>0</td>
      <td>1.60</td>
      <td>9.35</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>0.2</td>
      <td>1.26</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>15</td>
      <td>1</td>
      <td>5</td>
      <td>4</td>
      <td>0</td>
      <td>0.2</td>
      <td>19.48</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>1</td>
      <td>C</td>
    </tr>
    <tr>
      <th>2</th>
      <td>양효진</td>
      <td>18</td>
      <td>32</td>
      <td>15</td>
      <td>2</td>
      <td>2</td>
      <td>46.88</td>
      <td>20.92</td>
      <td>20</td>
      <td>9</td>
      <td>1</td>
      <td>0</td>
      <td>45.00</td>
      <td>21.74</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>12</td>
      <td>6</td>
      <td>1</td>
      <td>2</td>
      <td>50.0</td>
      <td>66.67</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>21</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>20.19</td>
      <td>10</td>
      <td>7</td>
      <td>3</td>
      <td>0</td>
      <td>1.40</td>
      <td>9.35</td>
      <td>3</td>
      <td>2</td>
      <td>0</td>
      <td>0.4</td>
      <td>1.89</td>
      <td>3</td>
      <td>1</td>
      <td>0</td>
      <td>33.33</td>
      <td>3.30</td>
      <td>13</td>
      <td>3</td>
      <td>2</td>
      <td>5</td>
      <td>0</td>
      <td>0.6</td>
      <td>16.88</td>
      <td>5</td>
      <td>0</td>
      <td>3</td>
      <td>1</td>
      <td>C</td>
    </tr>
    <tr>
      <th>3</th>
      <td>고예림</td>
      <td>8</td>
      <td>19</td>
      <td>6</td>
      <td>3</td>
      <td>2</td>
      <td>31.58</td>
      <td>12.42</td>
      <td>9</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>22.22</td>
      <td>9.78</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>7.69</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>8</td>
      <td>3</td>
      <td>1</td>
      <td>2</td>
      <td>37.50</td>
      <td>27.59</td>
      <td>15</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>14.42</td>
      <td>16</td>
      <td>14</td>
      <td>2</td>
      <td>0</td>
      <td>2.80</td>
      <td>14.95</td>
      <td>7</td>
      <td>2</td>
      <td>0</td>
      <td>0.4</td>
      <td>4.40</td>
      <td>21</td>
      <td>10</td>
      <td>0</td>
      <td>47.62</td>
      <td>23.08</td>
      <td>8</td>
      <td>2</td>
      <td>3</td>
      <td>1</td>
      <td>0</td>
      <td>0.4</td>
      <td>10.39</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>1</td>
      <td>L</td>
    </tr>
    <tr>
      <th>4</th>
      <td>황민경</td>
      <td>2</td>
      <td>12</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>16.67</td>
      <td>7.84</td>
      <td>9</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>22.22</td>
      <td>9.78</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>10.34</td>
      <td>15</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>14.42</td>
      <td>15</td>
      <td>13</td>
      <td>2</td>
      <td>0</td>
      <td>3.25</td>
      <td>14.02</td>
      <td>6</td>
      <td>2</td>
      <td>0</td>
      <td>0.5</td>
      <td>3.77</td>
      <td>33</td>
      <td>14</td>
      <td>5</td>
      <td>27.27</td>
      <td>36.26</td>
      <td>10</td>
      <td>0</td>
      <td>3</td>
      <td>6</td>
      <td>0</td>
      <td>0.0</td>
      <td>12.99</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>L</td>
    </tr>
    <tr>
      <th>5</th>
      <td>이다현</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>100.00</td>
      <td>1.31</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>100.00</td>
      <td>1.09</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>100.0</td>
      <td>5.56</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>2</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>1.92</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0.2</td>
      <td>0.63</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>7</td>
      <td>0</td>
      <td>4</td>
      <td>2</td>
      <td>0</td>
      <td>0.0</td>
      <td>9.09</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>C</td>
    </tr>
    <tr>
      <th>6</th>
      <td>김다인</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>22</td>
      <td>0</td>
      <td>3</td>
      <td>0.0</td>
      <td>21.15</td>
      <td>19</td>
      <td>15</td>
      <td>4</td>
      <td>0</td>
      <td>3.00</td>
      <td>17.76</td>
      <td>125</td>
      <td>50</td>
      <td>3</td>
      <td>10.0</td>
      <td>78.62</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>9</td>
      <td>1</td>
      <td>5</td>
      <td>3</td>
      <td>0</td>
      <td>0.2</td>
      <td>11.69</td>
      <td>1</td>
      <td>0</td>
      <td>6</td>
      <td>1</td>
      <td>S</td>
    </tr>
    <tr>
      <th>7</th>
      <td>김연견</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>10</td>
      <td>8</td>
      <td>2</td>
      <td>0</td>
      <td>1.60</td>
      <td>9.35</td>
      <td>4</td>
      <td>1</td>
      <td>0</td>
      <td>0.2</td>
      <td>2.52</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>Li</td>
    </tr>
    <tr>
      <th>8</th>
      <td>김주하</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>16</td>
      <td>14</td>
      <td>2</td>
      <td>0</td>
      <td>2.80</td>
      <td>14.95</td>
      <td>4</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>2.52</td>
      <td>22</td>
      <td>10</td>
      <td>3</td>
      <td>31.82</td>
      <td>24.18</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>Li</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 선수 별 득점 점유율 구하기 (개인 별 득점을 팀 전체의 득점으로 나누기)

team1_total_score = team1['득점_득점'].sum()
team2_total_score = team2['득점_득점'].sum()
team1['득점점유율'] = team1['득점_득점'] / team1_total_score
team2['득점점유율'] = team2['득점_득점'] / team2_total_score
team1
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>이름</th>
      <th>득점_득점</th>
      <th>공격종합_시도</th>
      <th>공격종합_성공</th>
      <th>공격종합_공격차단</th>
      <th>공격종합_범실</th>
      <th>공격종합_성공률</th>
      <th>공격종합_점유율</th>
      <th>오픈_시도</th>
      <th>오픈_성공</th>
      <th>오픈_공격차단</th>
      <th>오픈_범실</th>
      <th>오픈_성공률</th>
      <th>오픈_점유율</th>
      <th>시간차_시도</th>
      <th>시간차_성공</th>
      <th>시간차_공격차단</th>
      <th>시간차_범실</th>
      <th>시간차_성공률</th>
      <th>시간차_점유율</th>
      <th>이동_시도</th>
      <th>이동_성공</th>
      <th>이동_공격차단</th>
      <th>이동_범실</th>
      <th>이동_성공률</th>
      <th>이동_점유율</th>
      <th>후위_시도</th>
      <th>후위_성공</th>
      <th>후위_공격차단</th>
      <th>후위_범실</th>
      <th>후위_성공률</th>
      <th>후위_점유율</th>
      <th>속공_시도</th>
      <th>속공_성공</th>
      <th>속공_공격차단</th>
      <th>속공_범실</th>
      <th>속공_성공률</th>
      <th>속공_점유율</th>
      <th>퀵오픈_시도</th>
      <th>퀵오픈_성공</th>
      <th>퀵오픈_공격차단</th>
      <th>퀵오픈_범실</th>
      <th>퀵오픈_성공률</th>
      <th>퀵오픈_점유율</th>
      <th>서브_시도</th>
      <th>서브_성공</th>
      <th>서브_범실</th>
      <th>서브_성공률</th>
      <th>서브_점유율</th>
      <th>디그_시도</th>
      <th>디그_성공</th>
      <th>디그_실패</th>
      <th>디그_범실</th>
      <th>디그_세트당</th>
      <th>디그_점유율</th>
      <th>세트_시도</th>
      <th>세트_성공</th>
      <th>세트_범실</th>
      <th>세트_세트당</th>
      <th>세트_점유율</th>
      <th>리시브_시도</th>
      <th>리시브_정확</th>
      <th>리시브_실패</th>
      <th>리시브_세트당</th>
      <th>리시브_점유율</th>
      <th>블로킹_시도</th>
      <th>블로킹_성공</th>
      <th>블로킹_유효블락</th>
      <th>블로킹_실패</th>
      <th>블로킹_범실</th>
      <th>블로킹_세트당</th>
      <th>블로킹_점유율</th>
      <th>블로킹_어시스트</th>
      <th>벌칙_벌칙</th>
      <th>범실_범실</th>
      <th>스타팅멤버</th>
      <th>포지션</th>
      <th>득점점유율</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>루소</td>
      <td>28</td>
      <td>54</td>
      <td>22</td>
      <td>3</td>
      <td>3</td>
      <td>40.74</td>
      <td>35.29</td>
      <td>27</td>
      <td>10</td>
      <td>3</td>
      <td>2</td>
      <td>37.04</td>
      <td>29.35</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>12</td>
      <td>7</td>
      <td>0</td>
      <td>0</td>
      <td>58.33</td>
      <td>92.31</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>15</td>
      <td>5</td>
      <td>0</td>
      <td>1</td>
      <td>33.33</td>
      <td>51.72</td>
      <td>16</td>
      <td>2</td>
      <td>3</td>
      <td>0.4</td>
      <td>15.38</td>
      <td>11</td>
      <td>8</td>
      <td>3</td>
      <td>0</td>
      <td>1.60</td>
      <td>10.28</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>1.89</td>
      <td>12</td>
      <td>2</td>
      <td>3</td>
      <td>0.00</td>
      <td>13.19</td>
      <td>15</td>
      <td>4</td>
      <td>2</td>
      <td>5</td>
      <td>0</td>
      <td>0.8</td>
      <td>19.48</td>
      <td>2</td>
      <td>0</td>
      <td>6</td>
      <td>1</td>
      <td>L</td>
      <td>0.3500</td>
    </tr>
    <tr>
      <th>1</th>
      <td>정지윤</td>
      <td>21</td>
      <td>34</td>
      <td>20</td>
      <td>3</td>
      <td>1</td>
      <td>58.82</td>
      <td>22.22</td>
      <td>26</td>
      <td>16</td>
      <td>2</td>
      <td>1</td>
      <td>61.54</td>
      <td>28.26</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>5</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>40.0</td>
      <td>27.78</td>
      <td>3</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>66.67</td>
      <td>10.34</td>
      <td>9</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>8.65</td>
      <td>10</td>
      <td>8</td>
      <td>2</td>
      <td>0</td>
      <td>1.60</td>
      <td>9.35</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>0.2</td>
      <td>1.26</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>15</td>
      <td>1</td>
      <td>5</td>
      <td>4</td>
      <td>0</td>
      <td>0.2</td>
      <td>19.48</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>1</td>
      <td>C</td>
      <td>0.2625</td>
    </tr>
    <tr>
      <th>2</th>
      <td>양효진</td>
      <td>18</td>
      <td>32</td>
      <td>15</td>
      <td>2</td>
      <td>2</td>
      <td>46.88</td>
      <td>20.92</td>
      <td>20</td>
      <td>9</td>
      <td>1</td>
      <td>0</td>
      <td>45.00</td>
      <td>21.74</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>12</td>
      <td>6</td>
      <td>1</td>
      <td>2</td>
      <td>50.0</td>
      <td>66.67</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>21</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>20.19</td>
      <td>10</td>
      <td>7</td>
      <td>3</td>
      <td>0</td>
      <td>1.40</td>
      <td>9.35</td>
      <td>3</td>
      <td>2</td>
      <td>0</td>
      <td>0.4</td>
      <td>1.89</td>
      <td>3</td>
      <td>1</td>
      <td>0</td>
      <td>33.33</td>
      <td>3.30</td>
      <td>13</td>
      <td>3</td>
      <td>2</td>
      <td>5</td>
      <td>0</td>
      <td>0.6</td>
      <td>16.88</td>
      <td>5</td>
      <td>0</td>
      <td>3</td>
      <td>1</td>
      <td>C</td>
      <td>0.2250</td>
    </tr>
    <tr>
      <th>3</th>
      <td>고예림</td>
      <td>8</td>
      <td>19</td>
      <td>6</td>
      <td>3</td>
      <td>2</td>
      <td>31.58</td>
      <td>12.42</td>
      <td>9</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>22.22</td>
      <td>9.78</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>7.69</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>8</td>
      <td>3</td>
      <td>1</td>
      <td>2</td>
      <td>37.50</td>
      <td>27.59</td>
      <td>15</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>14.42</td>
      <td>16</td>
      <td>14</td>
      <td>2</td>
      <td>0</td>
      <td>2.80</td>
      <td>14.95</td>
      <td>7</td>
      <td>2</td>
      <td>0</td>
      <td>0.4</td>
      <td>4.40</td>
      <td>21</td>
      <td>10</td>
      <td>0</td>
      <td>47.62</td>
      <td>23.08</td>
      <td>8</td>
      <td>2</td>
      <td>3</td>
      <td>1</td>
      <td>0</td>
      <td>0.4</td>
      <td>10.39</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>1</td>
      <td>L</td>
      <td>0.1000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>황민경</td>
      <td>2</td>
      <td>12</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>16.67</td>
      <td>7.84</td>
      <td>9</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>22.22</td>
      <td>9.78</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>10.34</td>
      <td>15</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>14.42</td>
      <td>15</td>
      <td>13</td>
      <td>2</td>
      <td>0</td>
      <td>3.25</td>
      <td>14.02</td>
      <td>6</td>
      <td>2</td>
      <td>0</td>
      <td>0.5</td>
      <td>3.77</td>
      <td>33</td>
      <td>14</td>
      <td>5</td>
      <td>27.27</td>
      <td>36.26</td>
      <td>10</td>
      <td>0</td>
      <td>3</td>
      <td>6</td>
      <td>0</td>
      <td>0.0</td>
      <td>12.99</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>L</td>
      <td>0.0250</td>
    </tr>
    <tr>
      <th>5</th>
      <td>이다현</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>100.00</td>
      <td>1.31</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>100.00</td>
      <td>1.09</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>100.0</td>
      <td>5.56</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>2</td>
      <td>0</td>
      <td>1</td>
      <td>0.0</td>
      <td>1.92</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0.2</td>
      <td>0.63</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>7</td>
      <td>0</td>
      <td>4</td>
      <td>2</td>
      <td>0</td>
      <td>0.0</td>
      <td>9.09</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>C</td>
      <td>0.0250</td>
    </tr>
    <tr>
      <th>6</th>
      <td>김다인</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>22</td>
      <td>0</td>
      <td>3</td>
      <td>0.0</td>
      <td>21.15</td>
      <td>19</td>
      <td>15</td>
      <td>4</td>
      <td>0</td>
      <td>3.00</td>
      <td>17.76</td>
      <td>125</td>
      <td>50</td>
      <td>3</td>
      <td>10.0</td>
      <td>78.62</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>9</td>
      <td>1</td>
      <td>5</td>
      <td>3</td>
      <td>0</td>
      <td>0.2</td>
      <td>11.69</td>
      <td>1</td>
      <td>0</td>
      <td>6</td>
      <td>1</td>
      <td>S</td>
      <td>0.0125</td>
    </tr>
    <tr>
      <th>7</th>
      <td>김연견</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>10</td>
      <td>8</td>
      <td>2</td>
      <td>0</td>
      <td>1.60</td>
      <td>9.35</td>
      <td>4</td>
      <td>1</td>
      <td>0</td>
      <td>0.2</td>
      <td>2.52</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>Li</td>
      <td>0.0000</td>
    </tr>
    <tr>
      <th>8</th>
      <td>김주하</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>16</td>
      <td>14</td>
      <td>2</td>
      <td>0</td>
      <td>2.80</td>
      <td>14.95</td>
      <td>4</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>2.52</td>
      <td>22</td>
      <td>10</td>
      <td>3</td>
      <td>31.82</td>
      <td>24.18</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>Li</td>
      <td>0.0000</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 컬럼 선택

columns = [
    '이름',
#     '득점_득점',
#     '공격종합_시도',
#     '공격종합_성공',
#     '공격종합_공격차단',
#     '공격종합_범실',
    '공격종합_성공률',
#     '공격종합_점유율',
#     '오픈_시도',
#     '오픈_성공',
#     '오픈_공격차단',
#     '오픈_범실',
    '오픈_성공률',
#     '오픈_점유율',
#     '시간차_시도',
#     '시간차_성공',
#     '시간차_공격차단',
#     '시간차_범실',
    '시간차_성공률',
#     '시간차_점유율',
#     '이동_시도',
#     '이동_성공',
#     '이동_공격차단',
#     '이동_범실',
    '이동_성공률',
#     '이동_점유율',
#     '후위_시도',
#     '후위_성공',
#     '후위_공격차단',
#     '후위_범실',
    '후위_성공률',
#     '후위_점유율',
#     '속공_시도',
#     '속공_성공',
#     '속공_공격차단',
#     '속공_범실',
    '속공_성공률',
#     '속공_점유율',
#     '퀵오픈_시도',
#     '퀵오픈_성공',
#     '퀵오픈_공격차단',
#     '퀵오픈_범실',
    '퀵오픈_성공률',
#     '퀵오픈_점유율',
#     '서브_시도',
#     '서브_성공',
#     '서브_범실',
    '서브_성공률',
#     '서브_점유율',
#     '디그_시도',
#     '디그_성공',
#     '디그_실패',
#     '디그_범실',
#     '디그_세트당',
#     '디그_점유율',
#     '세트_시도',
#     '세트_성공',
#     '세트_범실',
#     '세트_세트당',
#     '세트_점유율',
#     '리시브_시도',
#     '리시브_정확',
#     '리시브_실패',
#     '리시브_세트당',
#     '리시브_점유율',
#     '블로킹_시도',
#     '블로킹_성공',
#     '블로킹_유효블락',
#     '블로킹_실패',
#     '블로킹_범실',
#     '블로킹_세트당',
#     '블로킹_점유율',
#     '블로킹_어시스트',
#     '벌칙_벌칙',
    '범실_범실',
    '포지션',
    '득점점유율',
    '스타팅멤버'
]


team1 = team1[columns]
team2 = team2[columns]
team1
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>이름</th>
      <th>공격종합_성공률</th>
      <th>오픈_성공률</th>
      <th>시간차_성공률</th>
      <th>이동_성공률</th>
      <th>후위_성공률</th>
      <th>속공_성공률</th>
      <th>퀵오픈_성공률</th>
      <th>서브_성공률</th>
      <th>범실_범실</th>
      <th>포지션</th>
      <th>득점점유율</th>
      <th>스타팅멤버</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>루소</td>
      <td>40.74</td>
      <td>37.04</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>58.33</td>
      <td>0.0</td>
      <td>33.33</td>
      <td>0.4</td>
      <td>6</td>
      <td>L</td>
      <td>0.3500</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>정지윤</td>
      <td>58.82</td>
      <td>61.54</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>40.0</td>
      <td>66.67</td>
      <td>0.0</td>
      <td>2</td>
      <td>C</td>
      <td>0.2625</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>양효진</td>
      <td>46.88</td>
      <td>45.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>50.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>3</td>
      <td>C</td>
      <td>0.2250</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>고예림</td>
      <td>31.58</td>
      <td>22.22</td>
      <td>100.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>37.50</td>
      <td>0.0</td>
      <td>2</td>
      <td>L</td>
      <td>0.1000</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>황민경</td>
      <td>16.67</td>
      <td>22.22</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>1</td>
      <td>L</td>
      <td>0.0250</td>
      <td>1</td>
    </tr>
    <tr>
      <th>5</th>
      <td>이다현</td>
      <td>100.00</td>
      <td>100.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>100.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>1</td>
      <td>C</td>
      <td>0.0250</td>
      <td>0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>김다인</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>6</td>
      <td>S</td>
      <td>0.0125</td>
      <td>1</td>
    </tr>
    <tr>
      <th>7</th>
      <td>김연견</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0</td>
      <td>Li</td>
      <td>0.0000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>김주하</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0</td>
      <td>Li</td>
      <td>0.0000</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 경기날짜, 팀명, 결과 정보 추가
team1['팀명'] = set_result.iloc[0,0]
team2['팀명'] = set_result.iloc[1,0]
team1['결과'] = 1 if int(set_result.iloc[0,-1]) > int(set_result.iloc[1,-1]) else 0
team2['결과'] = 0 if int(set_result.iloc[0,-1]) > int(set_result.iloc[1,-1]) else 1
team1['경기날짜'] = date
team2['경기날짜'] = date

# 컬럼 순서 변경
team1 = team1[['팀명', '경기날짜', '결과'] + columns]
team2 = team2[['팀명', '경기날짜', '결과'] + columns]
team1
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>팀명</th>
      <th>경기날짜</th>
      <th>결과</th>
      <th>이름</th>
      <th>공격종합_성공률</th>
      <th>오픈_성공률</th>
      <th>시간차_성공률</th>
      <th>이동_성공률</th>
      <th>후위_성공률</th>
      <th>속공_성공률</th>
      <th>퀵오픈_성공률</th>
      <th>서브_성공률</th>
      <th>범실_범실</th>
      <th>포지션</th>
      <th>득점점유율</th>
      <th>스타팅멤버</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>현대건설</td>
      <td>2020-10-17</td>
      <td>1</td>
      <td>루소</td>
      <td>40.74</td>
      <td>37.04</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>58.33</td>
      <td>0.0</td>
      <td>33.33</td>
      <td>0.4</td>
      <td>6</td>
      <td>L</td>
      <td>0.3500</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>현대건설</td>
      <td>2020-10-17</td>
      <td>1</td>
      <td>정지윤</td>
      <td>58.82</td>
      <td>61.54</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>40.0</td>
      <td>66.67</td>
      <td>0.0</td>
      <td>2</td>
      <td>C</td>
      <td>0.2625</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>현대건설</td>
      <td>2020-10-17</td>
      <td>1</td>
      <td>양효진</td>
      <td>46.88</td>
      <td>45.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>50.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>3</td>
      <td>C</td>
      <td>0.2250</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>현대건설</td>
      <td>2020-10-17</td>
      <td>1</td>
      <td>고예림</td>
      <td>31.58</td>
      <td>22.22</td>
      <td>100.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>37.50</td>
      <td>0.0</td>
      <td>2</td>
      <td>L</td>
      <td>0.1000</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>현대건설</td>
      <td>2020-10-17</td>
      <td>1</td>
      <td>황민경</td>
      <td>16.67</td>
      <td>22.22</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>1</td>
      <td>L</td>
      <td>0.0250</td>
      <td>1</td>
    </tr>
    <tr>
      <th>5</th>
      <td>현대건설</td>
      <td>2020-10-17</td>
      <td>1</td>
      <td>이다현</td>
      <td>100.00</td>
      <td>100.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>100.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>1</td>
      <td>C</td>
      <td>0.0250</td>
      <td>0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>현대건설</td>
      <td>2020-10-17</td>
      <td>1</td>
      <td>김다인</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>6</td>
      <td>S</td>
      <td>0.0125</td>
      <td>1</td>
    </tr>
    <tr>
      <th>7</th>
      <td>현대건설</td>
      <td>2020-10-17</td>
      <td>1</td>
      <td>김연견</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0</td>
      <td>Li</td>
      <td>0.0000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>현대건설</td>
      <td>2020-10-17</td>
      <td>1</td>
      <td>김주하</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0</td>
      <td>Li</td>
      <td>0.0000</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 연결
team = pd.concat([team1, team2])
team
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>팀명</th>
      <th>경기날짜</th>
      <th>결과</th>
      <th>이름</th>
      <th>공격종합_성공률</th>
      <th>오픈_성공률</th>
      <th>시간차_성공률</th>
      <th>이동_성공률</th>
      <th>후위_성공률</th>
      <th>속공_성공률</th>
      <th>퀵오픈_성공률</th>
      <th>서브_성공률</th>
      <th>범실_범실</th>
      <th>포지션</th>
      <th>득점점유율</th>
      <th>스타팅멤버</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>현대건설</td>
      <td>2020-10-17</td>
      <td>1</td>
      <td>루소</td>
      <td>40.74</td>
      <td>37.04</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>58.33</td>
      <td>0.00</td>
      <td>33.33</td>
      <td>0.40</td>
      <td>6</td>
      <td>L</td>
      <td>0.3500</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>현대건설</td>
      <td>2020-10-17</td>
      <td>1</td>
      <td>정지윤</td>
      <td>58.82</td>
      <td>61.54</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>40.00</td>
      <td>66.67</td>
      <td>0.00</td>
      <td>2</td>
      <td>C</td>
      <td>0.2625</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>현대건설</td>
      <td>2020-10-17</td>
      <td>1</td>
      <td>양효진</td>
      <td>46.88</td>
      <td>45.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>50.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>3</td>
      <td>C</td>
      <td>0.2250</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>현대건설</td>
      <td>2020-10-17</td>
      <td>1</td>
      <td>고예림</td>
      <td>31.58</td>
      <td>22.22</td>
      <td>100.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>37.50</td>
      <td>0.00</td>
      <td>2</td>
      <td>L</td>
      <td>0.1000</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>현대건설</td>
      <td>2020-10-17</td>
      <td>1</td>
      <td>황민경</td>
      <td>16.67</td>
      <td>22.22</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>1</td>
      <td>L</td>
      <td>0.0250</td>
      <td>1</td>
    </tr>
    <tr>
      <th>5</th>
      <td>현대건설</td>
      <td>2020-10-17</td>
      <td>1</td>
      <td>이다현</td>
      <td>100.00</td>
      <td>100.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>100.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>1</td>
      <td>C</td>
      <td>0.0250</td>
      <td>0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>현대건설</td>
      <td>2020-10-17</td>
      <td>1</td>
      <td>김다인</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>6</td>
      <td>S</td>
      <td>0.0125</td>
      <td>1</td>
    </tr>
    <tr>
      <th>7</th>
      <td>현대건설</td>
      <td>2020-10-17</td>
      <td>1</td>
      <td>김연견</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>Li</td>
      <td>0.0000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>현대건설</td>
      <td>2020-10-17</td>
      <td>1</td>
      <td>김주하</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>Li</td>
      <td>0.0000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>0</th>
      <td>GS칼텍스</td>
      <td>2020-10-17</td>
      <td>0</td>
      <td>러츠</td>
      <td>39.39</td>
      <td>32.43</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>50.00</td>
      <td>0.00</td>
      <td>46.67</td>
      <td>0.40</td>
      <td>10</td>
      <td>R</td>
      <td>0.4125</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>GS칼텍스</td>
      <td>2020-10-17</td>
      <td>0</td>
      <td>강소휘</td>
      <td>51.35</td>
      <td>53.33</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>52.38</td>
      <td>0.40</td>
      <td>4</td>
      <td>L</td>
      <td>0.2625</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>GS칼텍스</td>
      <td>2020-10-17</td>
      <td>0</td>
      <td>이소영</td>
      <td>24.14</td>
      <td>29.41</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>18.18</td>
      <td>0.20</td>
      <td>2</td>
      <td>L</td>
      <td>0.1125</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>GS칼텍스</td>
      <td>2020-10-17</td>
      <td>0</td>
      <td>한수지</td>
      <td>40.00</td>
      <td>50.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>33.33</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>C</td>
      <td>0.0875</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>GS칼텍스</td>
      <td>2020-10-17</td>
      <td>0</td>
      <td>안혜진</td>
      <td>100.00</td>
      <td>100.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.80</td>
      <td>2</td>
      <td>S</td>
      <td>0.0750</td>
      <td>1</td>
    </tr>
    <tr>
      <th>5</th>
      <td>GS칼텍스</td>
      <td>2020-10-17</td>
      <td>0</td>
      <td>김유리</td>
      <td>37.50</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>42.86</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>C</td>
      <td>0.0375</td>
      <td>1</td>
    </tr>
    <tr>
      <th>7</th>
      <td>GS칼텍스</td>
      <td>2020-10-17</td>
      <td>0</td>
      <td>유서연</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.33</td>
      <td>2</td>
      <td>R</td>
      <td>0.0125</td>
      <td>0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>GS칼텍스</td>
      <td>2020-10-17</td>
      <td>0</td>
      <td>이현</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>1</td>
      <td>S</td>
      <td>0.0000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>GS칼텍스</td>
      <td>2020-10-17</td>
      <td>0</td>
      <td>한다혜</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>Li</td>
      <td>0.0000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>GS칼텍스</td>
      <td>2020-10-17</td>
      <td>0</td>
      <td>한수진</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0</td>
      <td>Li</td>
      <td>0.0000</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

# 위의 과정을 함수화
- get_result
    - parameter : driver
    - return : set_result, date, date, team1, team2
- clean_result
    - parameter : set_result, date, date, team1, team2
    - return : team


```python
def get_result(driver) :
    #--------------------- table 태그 읽기 - 팀 별 1 ~ 5 세트 결과 ---------------------#
    html = driver.page_source
    soup = BeautifulSoup(html, 'html.parser')
    table = soup.find_all('table')
    set_result = pd.read_html(str(table))[1] # 1 ~ 5 세트 별 득점 결과

    #--------------------- 날짜 정보 읽기 ---------------------#
    date_selector = '#wrp_content > article.wrp_recentgame.wrp_result > table > thead > tr > th'
    date = soup.select_one(date_selector).get_text()
    date = date.replace('\xa0', '').split('/')[0].strip().split()
    date = pd.to_datetime(date[0][:-1] + '/' + date[1][:-1] + '/' + date[2][:-1])

    #
    # 첫 번째 팀 정보 읽기
    #

    #--------------------- 버튼 클릭 - 선수 기록 ---------------------#
    x_path = '//*[@id="wrp_content"]/article[2]/ul/li[2]/a/span' # 선수 기록 버튼
    cursor = driver.find_element_by_xpath(x_path)
    cursor.click()
    time.sleep(1)

    #--------------------- table 태그 읽기 - 선수 별 출전 세트, 득점, 공격 종합 ---------------------#
    html = driver.page_source
    soup = BeautifulSoup(html, 'html.parser')
    table = soup.find_all('table')

    team1 = pd.read_html(str(table))[3] # 명단 table
    team1_info = pd.read_html(str(table))[4] # 출전 세트, 득점, 공격 종합 table
    team1_info.columns = ['_'.join(col).strip() for col in team1_info.columns]
    team1 = pd.concat([team1, team1_info], axis = 1)

    #--------------------- 나머지 기록 읽기 ---------------------#
    for _ in range(4) :
        #--------------------- 버튼 클릭 - 다음 기록 ---------------------#
        x_path = '//*[@id="team1"]/div/div[1]/a[2]' # 다음 기록 버튼
        cursor = driver.find_element_by_xpath(x_path)
        cursor.click()
        time.sleep(1)


        #--------------------- table 태그 읽기 - 다음 기록들 ---------------------#
        html = driver.page_source
        soup = BeautifulSoup(html, 'html.parser')
        table = soup.find_all('table')

        team1_info = pd.read_html(str(table))[4] # 다음 기록
        team1_info.columns = ['_'.join(col).strip() for col in team1_info.columns]
        team1 = pd.concat([team1, team1_info], axis = 1)


    #
    # 두 번째 팀 정보 읽기
    #

    #--------------------- 버튼 클릭 - 두 번째 팀 ---------------------#
    try :
        x_path = '//*[@id="tab2"]/div[4]/ul/li[2]/a/span' # 두 번째 팀 버튼
        cursor = driver.find_element_by_xpath(x_path)
    except:
        try :
            x_path = '//*[@id="tab2"]/div[3]/ul/li[2]/a/span' # 두 번째 팀 버튼
            cursor = driver.find_element_by_xpath(x_path)
        except :
            try :
                x_path = '//*[@id="tab2"]/div[2]/ul/li[2]/a/span' # 두 번째 팀 버튼
                cursor = driver.find_element_by_xpath(x_path)
            except :
                x_path = '//*[@id="tab2"]/div[1]/ul/li[2]/a/span' # 두 번째 팀 버튼
                cursor = driver.find_element_by_xpath(x_path)

                
    cursor.click()
    time.sleep(1)

    #--------------------- table 태그 읽기 - 선수 별 출전 세트, 득점, 공격 종합 ---------------------#
    html = driver.page_source
    soup = BeautifulSoup(html, 'html.parser')
    table = soup.find_all('table')

    team2 = pd.read_html(str(table))[5] # 명단 table
    team2_info = pd.read_html(str(table))[6] # 출전 세트, 득점, 공격 종합 table
    team2_info.columns = ['_'.join(col).strip() for col in team2_info.columns]
    team2 = pd.concat([team2, team2_info], axis = 1)

    #--------------------- 나머지 기록 읽기 ---------------------#
    for _ in range(4) :
        #--------------------- 버튼 클릭 - 다음 기록 ---------------------#
        x_path = '//*[@id="team2"]/div/div[1]/a[2]' # 다음 기록 버튼
        cursor = driver.find_element_by_xpath(x_path)
        cursor.click()
        time.sleep(1)


        #--------------------- table 태그 읽기 - 다음 기록들 ---------------------#
        html = driver.page_source
        soup = BeautifulSoup(html, 'html.parser')
        table = soup.find_all('table')

        team2_info = pd.read_html(str(table))[6] # 다음 기록
        team2_info.columns = ['_'.join(col).strip() for col in team2_info.columns]
        team2 = pd.concat([team2, team2_info], axis = 1)
    
    return set_result, date, team1, team2
```


```python
def clean_result(set_result, date, team1, team2) :
    # No. 열 삭제
    team1 = team1.drop('No.', axis = 1)
    team2 = team2.drop('No.', axis = 1)

    
    # 3경기 이상 출전한 선수만 유지 (출전 세트 결측치가 3개 이상인 선수 제거)
    subset = ['출전세트_1set', '출전세트_2set', '출전세트_3set', '출전세트_4set', '출전세트_5set']

    # thresh = 3 : 정상 값(Not NaN)이 3개 이상만 유지
    team1 = team1.dropna(subset = subset, thresh = 3)
    team2 = team2.dropna(subset = subset, thresh = 3)

    # 스타팅멤버 표시 (3세트 이상 스타팅멤버라면 1 아니면 0)
    team1['스타팅멤버'] = 0
    team2['스타팅멤버'] = 0

    team1.loc[(team1.iloc[:, 1:6] == 'O').sum(axis = 1) >= 3, '스타팅멤버'] = 1
    team2.loc[(team2.iloc[:, 1:6] == 'O').sum(axis = 1) >= 3, '스타팅멤버'] = 1
    
    
    # 출전 세트 열 삭제
    subset = ['출전세트_1set', '출전세트_2set', '출전세트_3set', '출전세트_4set', '출전세트_5set']

    team1 = team1.drop(subset, axis = 1)
    team2 = team2.drop(subset, axis = 1)

    
    # 이름과 포지션 분리하기
    # "이름" 열 : 이름 정보
    # "포지션" 열 : 포지션 정보
    team1['포지션'] = team1['이름'].str.split().str[1].str.strip('()')
    team1['이름'] = team1['이름'].str.split().str[0]

    team2['포지션'] = team2['이름'].str.split().str[1].str.strip('()')
    team2['이름'] = team2['이름'].str.split().str[0]

    
    # 선수 별 득점 점유율 구하기 (개인 별 득점을 팀 전체의 득점으로 나누기)
    team1_total_score = team1['득점_득점'].sum()
    team2_total_score = team2['득점_득점'].sum()
    team1['득점점유율'] = team1['득점_득점'] / team1_total_score
    team2['득점점유율'] = team2['득점_득점'] / team2_total_score
    
    
    # 컬럼 선택
    columns = [
        '이름',
    #     '득점_득점',
    #     '공격종합_시도',
    #     '공격종합_성공',
    #     '공격종합_공격차단',
    #     '공격종합_범실',
        '공격종합_성공률',
    #     '공격종합_점유율',
#         '오픈_시도',
#         '오픈_성공',
    #     '오픈_공격차단',
    #     '오픈_범실',
        '오픈_성공률',
    #     '오픈_점유율',
    #     '시간차_시도',
    #     '시간차_성공',
    #     '시간차_공격차단',
    #     '시간차_범실',
#         '시간차_성공률',
    #     '시간차_점유율',
    #     '이동_시도',
    #     '이동_성공',
    #     '이동_공격차단',
    #     '이동_범실',
#         '이동_성공률',
    #     '이동_점유율',
    #     '후위_시도',
    #     '후위_성공',
    #     '후위_공격차단',
    #     '후위_범실',
        '후위_성공률',
    #     '후위_점유율',
    #     '속공_시도',
    #     '속공_성공',
    #     '속공_공격차단',
    #     '속공_범실',
        '속공_성공률',
    #     '속공_점유율',
    #     '퀵오픈_시도',
    #     '퀵오픈_성공',
    #     '퀵오픈_공격차단',
    #     '퀵오픈_범실',
        '퀵오픈_성공률',
    #     '퀵오픈_점유율',
    #     '서브_시도',
    #     '서브_성공',
    #     '서브_범실',
        '서브_성공률',
    #     '서브_점유율',
        '디그_시도',
        '디그_성공',
    #     '디그_실패',
    #     '디그_범실',
    #     '디그_세트당',
    #     '디그_점유율',
        '세트_시도',
        '세트_성공',
    #     '세트_범실',
    #     '세트_세트당',
    #     '세트_점유율',
    #     '리시브_시도',
    #     '리시브_정확',
    #     '리시브_실패',
    #     '리시브_세트당',
    #     '리시브_점유율',
        '블로킹_시도',
        '블로킹_성공',
    #     '블로킹_유효블락',
    #     '블로킹_실패',
    #     '블로킹_범실',
    #     '블로킹_세트당',
    #     '블로킹_점유율',
    #     '블로킹_어시스트',
    #     '벌칙_벌칙',
        '범실_범실',
        '포지션',
        '득점점유율',
        '스타팅멤버'
    ]
    team1 = team1[columns]
    team2 = team2[columns]
    
    
    # 경기날짜, 팀명, 결과 정보 추가
    team1['팀명'] = set_result.iloc[0,0]
    team2['팀명'] = set_result.iloc[1,0]
    team1['결과'] = 1 if int(set_result.iloc[0,-1]) > int(set_result.iloc[1,-1]) else 0
    team2['결과'] = 0 if int(set_result.iloc[0,-1]) > int(set_result.iloc[1,-1]) else 1
    team1['경기날짜'] = date
    team2['경기날짜'] = date

    # 컬럼 순서 변경
    team1 = team1[['팀명', '경기날짜', '결과'] + columns]
    team2 = team2[['팀명', '경기날짜', '결과'] + columns]
    
    
    # 연결
    team = pd.concat([team1, team2])
    team
    
    return team
```

<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

# 모든 경기의 정보 가져오기
- 경기 스케쥴 url 확인
- 여자 배구 상세결과 url 확인
- 각 url 별 함수 적용
- 파일로 저장

#### 경기 스케쥴 url 확인
- 도드람 2017-2018 V-리그 url
    - https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=014&team=&yymm=2017-10&r_round=
    - ...
    - https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=014&team=&yymm=2018-03&r_round=
    
- 도드람 2018-2019 V-리그 url
    - https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=015&team=&yymm=2018-10&r_round=
    - ...
    - https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=015&team=&yymm=2019-03&r_round=
    
- 도드람 2018-2020 V-리그 url
    - https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=016&team=&yymm=2019-10&r_round=
    - ...
    - https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=016&team=&yymm=2020-03&r_round=

- 도드람 2020-2021 V-리그 url
    - https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=017&team=&yymm=2020-10&r_round=
    - ...
    - https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=017&team=&yymm=2021-04&r_round=

- 도드람 2021-2022 V-리그 url
    - https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=018&team=&yymm=2021-10&r_round=
    - ...
    - https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=018&team=&yymm=2022-02&r_round=


```python
# 반복문으로 각 url 출력
seasons = [f'0{num}' for num in range(14, 19)]
yymms = [
pd.date_range('2017-10', '2018-04', freq = 'M').strftime('%Y-%m').to_list(),
pd.date_range('2018-10', '2019-04', freq = 'M').strftime('%Y-%m').to_list(),
pd.date_range('2019-10', '2020-04', freq = 'M').strftime('%Y-%m').to_list(),
pd.date_range('2020-10', '2021-05', freq = 'M').strftime('%Y-%m').to_list(),
pd.date_range('2021-10', '2022-03', freq = 'M').strftime('%Y-%m').to_list()
]
# print(seasons)
# print(yymm)

for season, dates in zip(seasons, yymms) :
    for date in dates :
        url = f'https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season={season}&team=&yymm={date}&r_round='
        print(url)
```

    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=014&team=&yymm=2017-10&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=014&team=&yymm=2017-11&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=014&team=&yymm=2017-12&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=014&team=&yymm=2018-01&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=014&team=&yymm=2018-02&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=014&team=&yymm=2018-03&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=015&team=&yymm=2018-10&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=015&team=&yymm=2018-11&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=015&team=&yymm=2018-12&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=015&team=&yymm=2019-01&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=015&team=&yymm=2019-02&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=015&team=&yymm=2019-03&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=016&team=&yymm=2019-10&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=016&team=&yymm=2019-11&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=016&team=&yymm=2019-12&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=016&team=&yymm=2020-01&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=016&team=&yymm=2020-02&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=016&team=&yymm=2020-03&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=017&team=&yymm=2020-10&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=017&team=&yymm=2020-11&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=017&team=&yymm=2020-12&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=017&team=&yymm=2021-01&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=017&team=&yymm=2021-02&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=017&team=&yymm=2021-03&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=017&team=&yymm=2021-04&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=018&team=&yymm=2021-10&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=018&team=&yymm=2021-11&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=018&team=&yymm=2021-12&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=018&team=&yymm=2022-01&r_round=
    https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=018&team=&yymm=2022-02&r_round=
    

<br/>
<br/>
<br/>
<br/>

#### 여자 배구 상세결과 url 확인
- 남자 / 여자 XPath 확인
- 상세결과 버튼 XPath 확인
- 경기가 없는 날의 XPath 확인


```python
# url - https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=014&team=&yymm=2017-10&r_round=
'''
//*[@id="type1"]/div/table/tbody/tr[1]/td[3] # 남자
//*[@id="type1"]/div/table/tbody/tr[1]/td[10]/a[2] # 상세결과


//*[@id="type1"]/div/table/tbody/tr[2]/td[3] # 여자
//*[@id="type1"]/div/table/tbody/tr[2]/td[10]/a[2] # 상세결과


//*[@id="type1"]/div/table/tbody/tr[3]/td[3] # 남자
//*[@id="type1"]/div/table/tbody/tr[3]/td[10]/a[2] # 상세결과


//*[@id="type1"]/div/table/tbody/tr[4]/td[3] # 여자
//*[@id="type1"]/div/table/tbody/tr[4]/td[10]/a[2] # 상세결과


//*[@id="type1"]/div/table/tbody/tr[5]/td[3] # 경기가 없습니다.


//*[@id="type1"]/div/table/tbody/tr[6]/td[3] # 여자
//*[@id="type1"]/div/table/tbody/tr[6]/td[10]/a[2] # 상세결과
...


'''
```




    '\n//*[@id="type1"]/div/table/tbody/tr[1]/td[3] # 남자\n//*[@id="type1"]/div/table/tbody/tr[1]/td[10]/a[2] # 상세결과\n\n\n//*[@id="type1"]/div/table/tbody/tr[2]/td[3] # 여자\n//*[@id="type1"]/div/table/tbody/tr[2]/td[10]/a[2] # 상세결과\n\n\n//*[@id="type1"]/div/table/tbody/tr[3]/td[3] # 남자\n//*[@id="type1"]/div/table/tbody/tr[3]/td[10]/a[2] # 상세결과\n\n\n//*[@id="type1"]/div/table/tbody/tr[4]/td[3] # 여자\n//*[@id="type1"]/div/table/tbody/tr[4]/td[10]/a[2] # 상세결과\n\n\n//*[@id="type1"]/div/table/tbody/tr[5]/td[3] # 경기가 없습니다.\n\n\n//*[@id="type1"]/div/table/tbody/tr[6]/td[3] # 여자\n//*[@id="type1"]/div/table/tbody/tr[6]/td[10]/a[2] # 상세결과\n...\n\n\n'




```python
# ## 여자 경기의 상세결과 접근하기

# # 경기 스케줄 url 접속
# driver = webdriver.Chrome()
# url = "https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=014&team=&yymm=2017-10&r_round="
# driver.get(url)
# time.sleep(2)


# # 경기 스케줄을 데이터프레임으로 저장 후 여자 경기의 인덱스를 idx에 저장
# html = driver.page_source
# soup = BeautifulSoup(html, 'html.parser')
# table = soup.find_all('table')
# df = pd.read_html(str(table))[0]
# idx = df[df['구분'] == '여자'].index.to_list()
# idx = [i + 1 for i in idx]

# dfs = []

# # 각 경기의 상세결과 버튼 클릭
# for i in idx :
#     # 상세결과 버튼 클릭
#     try :
#         x_path = f'//*[@id="type1"]/div/table/tbody/tr[{i}]/td[10]/a[3]' # 상세결과 버튼
#         cursor = driver.find_element_by_xpath(x_path)
#     except :
#         x_path = f'//*[@id="type1"]/div/table/tbody/tr[{i}]/td[10]/a[2]' # 상세결과 버튼
#         cursor = driver.find_element_by_xpath(x_path)
#     cursor.click()
#     time.sleep(1)
    
#     # 함수 적용
#     set_result, date, team1, team2 = get_result(driver)
#     team = clean_result(set_result, date, team1, team2)
#     dfs.append(team)
    
#     # 다시 경기 스케줄 url로 이동
#     driver.get(url)
#     time.sleep(1)
# df = pd.concat(dfs)
# df
```

<br/>
<br/>
<br/>
<br/>

#### 각 url 별 함수 적용


```python
dfs = []
driver = webdriver.Chrome()

# for season, dates in zip(seasons, yymms) :
for date in yymms[1]:
    # 경기 스케줄 url 접속
    url = f'https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season=015&team=&yymm={date}&r_round='
    driver.get(url)
    time.sleep(1)


    # 경기 스케줄을 데이터프레임으로 저장 후 여자 경기의 인덱스를 idx에 저장
    html = driver.page_source
    soup = BeautifulSoup(html, 'html.parser')
    table = soup.find_all('table')
    try :
        df = pd.read_html(str(table))[0]
        idx = df[df['구분'] == '남자'].index.to_list()
    except :
        df = pd.read_html(str(table))[2]
        idx = df[df['구분'] == '남자'].index.to_list()
    idx = [i + 1 for i in idx]



    # 각 경기의 상세결과 버튼 클릭
    for i in idx :
        print('첫번째 print {}'.format(i))
     
            
            
        # 상세결과 버튼 클릭
        try :
            x_path = f'//*[@id="type1"]/div/table/tbody/tr[{i}]/td[10]/a[3]' # 상세결과 버튼
            cursor = driver.find_element_by_xpath(x_path)
        except :
            x_path = f'//*[@id="type1"]/div/table/tbody/tr[{i}]/td[10]/a[2]' # 상세결과 버튼
            cursor = driver.find_element_by_xpath(x_path)
        cursor.click()
        time.sleep(1)

        # 함수 적용
        set_result, date, team1, team2 = get_result(driver)
        team = clean_result(set_result, date, team1, team2)
        dfs.append(team)

        # 다시 경기 스케줄 url로 이동
        driver.get(url)
        time.sleep(1)
# df = pd.concat(dfs)
# df

```

    첫번째 print 1
    첫번째 print 2
    첫번째 print 3
    첫번째 print 4
    첫번째 print 6
    첫번째 print 7
    첫번째 print 8
    첫번째 print 9
    첫번째 print 11
    첫번째 print 13
    첫번째 print 16
    첫번째 print 18
    첫번째 print 19
    첫번째 print 21
    첫번째 print 24
    첫번째 print 25
    첫번째 print 2
    첫번째 print 4
    첫번째 print 5
    첫번째 print 7
    첫번째 print 10
    첫번째 print 11
    첫번째 print 14
    첫번째 print 15
    첫번째 print 16
    첫번째 print 18
    첫번째 print 20
    첫번째 print 21
    첫번째 print 24
    첫번째 print 25
    첫번째 print 26
    첫번째 print 28
    첫번째 print 30
    첫번째 print 31
    첫번째 print 34
    첫번째 print 35
    첫번째 print 36
    첫번째 print 38
    첫번째 print 40
    첫번째 print 41
    첫번째 print 45
    첫번째 print 46
    첫번째 print 1
    첫번째 print 3
    첫번째 print 5
    첫번째 print 6
    첫번째 print 9
    첫번째 print 10
    첫번째 print 11
    첫번째 print 13
    첫번째 print 15
    첫번째 print 16
    첫번째 print 19
    첫번째 print 20
    첫번째 print 21
    첫번째 print 23
    첫번째 print 25
    첫번째 print 26
    첫번째 print 29
    첫번째 print 30
    첫번째 print 31
    첫번째 print 33
    첫번째 print 36
    첫번째 print 38
    첫번째 print 40
    첫번째 print 41
    첫번째 print 42
    첫번째 print 44
    첫번째 print 46
    첫번째 print 1
    첫번째 print 5
    첫번째 print 6
    첫번째 print 7
    첫번째 print 9
    첫번째 print 11
    첫번째 print 12
    첫번째 print 15
    첫번째 print 16
    첫번째 print 17
    첫번째 print 19
    첫번째 print 21
    첫번째 print 22
    첫번째 print 25
    첫번째 print 26
    첫번째 print 33
    첫번째 print 34
    첫번째 print 35
    첫번째 print 37
    첫번째 print 39
    첫번째 print 40
    첫번째 print 43
    첫번째 print 1
    첫번째 print 2
    첫번째 print 4
    첫번째 print 6
    첫번째 print 8
    첫번째 print 10
    첫번째 print 12
    첫번째 print 13
    첫번째 print 14
    첫번째 print 16
    첫번째 print 18
    첫번째 print 19
    첫번째 print 23
    첫번째 print 24
    첫번째 print 25
    첫번째 print 27
    첫번째 print 29
    첫번째 print 30
    첫번째 print 33
    첫번째 print 34
    첫번째 print 35
    첫번째 print 37
    첫번째 print 39
    첫번째 print 40
    첫번째 print 43
    첫번째 print 1
    첫번째 print 3
    첫번째 print 5
    첫번째 print 7
    첫번째 print 8
    첫번째 print 11
    첫번째 print 12
    첫번째 print 13
    첫번째 print 15
    첫번째 print 17
    첫번째 print 22
    첫번째 print 24
    첫번째 print 28
    첫번째 print 30
    첫번째 print 32
    


```python
df = pd.concat(dfs)
df.to_csv(f'volleyball_{season}.csv', index = False, encoding='utf-8')
df = pd.read_csv(f'volleyball_{season}.csv')
df
```

- 수동 실행 (시즌 별)


```python
# dfs = []
# season = seasons[4]
# dates = yymms[4]
# driver = webdriver.Chrome()

# for date in dates :
#     # 경기 스케줄 url 접속
#     url = f'https://www.kovo.co.kr/game/v-league/11110_schedule_list.asp?season={season}&team=&yymm={date}&r_round='
#     driver.get(url)
#     time.sleep(1)


#     # 경기 스케줄을 데이터프레임으로 저장 후 여자 경기의 인덱스를 idx에 저장
#     html = driver.page_source
#     soup = BeautifulSoup(html, 'html.parser')
#     table = soup.find_all('table')
#     try :
#         df = pd.read_html(str(table))[0]
#         idx = df[df['구분'] == '여자'].index.to_list()
#     except :
#         df = pd.read_html(str(table))[2]
#         idx = df[df['구분'] == '여자'].index.to_list()
#     idx = [i + 1 for i in idx]



#     # 각 경기의 상세결과 버튼 클릭
#     for i in idx :
#         # 상세결과 버튼 클릭
#         try :
#             x_path = f'//*[@id="type1"]/div/table/tbody/tr[{i}]/td[10]/a[3]' # 상세결과 버튼
#             cursor = driver.find_element_by_xpath(x_path)
#         except :
#             x_path = f'//*[@id="type1"]/div/table/tbody/tr[{i}]/td[10]/a[2]' # 상세결과 버튼
#             cursor = driver.find_element_by_xpath(x_path)
#         cursor = driver.find_element_by_xpath(x_path)
#         cursor.click()
#         time.sleep(1)

#         # 함수 적용
#         set_result, date, team1, team2 = get_result(driver)
#         team = clean_result(set_result, date, team1, team2)
#         dfs.append(team)

#         # 다시 경기 스케줄 url로 이동
#         driver.get(url)
#         time.sleep(1)
        
# # csv 파일로 저장
# df = pd.concat(dfs)
# df.to_csv(f'volleyball_{season}.csv', index = False, encoding='utf-8')
# df = pd.read_csv(f'volleyball_{season}.csv')
# df
```

<br/>
<br/>
<br/>
<br/>

#### 데이터 연결


```python
dfs = []
for season in seasons :
    dfs.append(pd.read_csv(f'./여자/volleyball_{season}.csv'))
df = pd.concat(dfs)
df.to_csv('volleyball.csv', index = False)
df = pd.read_csv('volleyball.csv')
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>팀명</th>
      <th>경기날짜</th>
      <th>결과</th>
      <th>이름</th>
      <th>공격종합_성공률</th>
      <th>오픈_성공률</th>
      <th>퀵오픈_성공률</th>
      <th>서브_성공률</th>
      <th>디그_시도</th>
      <th>디그_성공</th>
      <th>세트_시도</th>
      <th>세트_성공</th>
      <th>블로킹_시도</th>
      <th>블로킹_성공</th>
      <th>범실_범실</th>
      <th>포지션</th>
      <th>득점점유율</th>
      <th>스타팅멤버</th>
      <th>시간차_성공률</th>
      <th>이동_성공률</th>
      <th>후위_성공률</th>
      <th>속공_성공률</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>IBK기업은행</td>
      <td>2017-10-14</td>
      <td>0</td>
      <td>메디</td>
      <td>29.85</td>
      <td>26.83</td>
      <td>45.45</td>
      <td>0.20</td>
      <td>24.0</td>
      <td>20.0</td>
      <td>7.0</td>
      <td>0.0</td>
      <td>20.0</td>
      <td>3.0</td>
      <td>7</td>
      <td>L</td>
      <td>0.347826</td>
      <td>1</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>IBK기업은행</td>
      <td>2017-10-14</td>
      <td>0</td>
      <td>고예림</td>
      <td>30.00</td>
      <td>12.00</td>
      <td>60.00</td>
      <td>0.60</td>
      <td>22.0</td>
      <td>17.0</td>
      <td>7.0</td>
      <td>1.0</td>
      <td>10.0</td>
      <td>0.0</td>
      <td>6</td>
      <td>L</td>
      <td>0.217391</td>
      <td>1</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>IBK기업은행</td>
      <td>2017-10-14</td>
      <td>0</td>
      <td>김희진</td>
      <td>27.78</td>
      <td>20.00</td>
      <td>50.00</td>
      <td>0.20</td>
      <td>12.0</td>
      <td>11.0</td>
      <td>7.0</td>
      <td>0.0</td>
      <td>14.0</td>
      <td>4.0</td>
      <td>6</td>
      <td>R</td>
      <td>0.217391</td>
      <td>1</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>IBK기업은행</td>
      <td>2017-10-14</td>
      <td>0</td>
      <td>김미연</td>
      <td>31.25</td>
      <td>16.67</td>
      <td>60.00</td>
      <td>0.00</td>
      <td>6.0</td>
      <td>5.0</td>
      <td>3.0</td>
      <td>0.0</td>
      <td>12.0</td>
      <td>2.0</td>
      <td>1</td>
      <td>L</td>
      <td>0.101449</td>
      <td>1</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>IBK기업은행</td>
      <td>2017-10-14</td>
      <td>0</td>
      <td>김수지</td>
      <td>25.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.20</td>
      <td>11.0</td>
      <td>10.0</td>
      <td>7.0</td>
      <td>0.0</td>
      <td>28.0</td>
      <td>1.0</td>
      <td>3</td>
      <td>C</td>
      <td>0.086957</td>
      <td>1</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>8111</th>
      <td>KGC인삼공사</td>
      <td>2022-02-28</td>
      <td>0</td>
      <td>한송이</td>
      <td>28.57</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>C</td>
      <td>0.088889</td>
      <td>1</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>66.67</td>
    </tr>
    <tr>
      <th>8112</th>
      <td>KGC인삼공사</td>
      <td>2022-02-28</td>
      <td>0</td>
      <td>정호영</td>
      <td>60.00</td>
      <td>66.67</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>C</td>
      <td>0.066667</td>
      <td>1</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>50.00</td>
    </tr>
    <tr>
      <th>8113</th>
      <td>KGC인삼공사</td>
      <td>2022-02-28</td>
      <td>0</td>
      <td>고의정</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.33</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1</td>
      <td>L</td>
      <td>0.022222</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>8114</th>
      <td>KGC인삼공사</td>
      <td>2022-02-28</td>
      <td>0</td>
      <td>하효림</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.33</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>S</td>
      <td>0.022222</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>8115</th>
      <td>KGC인삼공사</td>
      <td>2022-02-28</td>
      <td>0</td>
      <td>채선아</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>Li</td>
      <td>0.000000</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.00</td>
    </tr>
  </tbody>
</table>
<p>8116 rows × 22 columns</p>
</div>


