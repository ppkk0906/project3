# 정형/비정형 데이터 활용 저평가 우량주 예측


## 사용 기술
- 사용 언어 : Python, JavaScript
- 개발 툴 : Jupyter Notebook, VScode
- API : Open Dart API
- 운영체제 : Windows 10
- 라이브러리 : Numpy, Pandas, Scikit-Learn, LightGBM, TensorFlow, Keras, BS4, Selenium, pykrx, TA-Lib, dart_fss, CanvasJS, ChartJS


## 코드 설명
![Project Flow Chart](https://user-images.githubusercontent.com/76696543/121051682-9d4dc780-c7f4-11eb-9b7e-7fb0b236c11a.png)


### 1. 저평가주 탐색 모듈
- 실행 코드 : find_undervalued_stock.ipynb
- 모듈 : undervaluedstock.py
```python
import undervaluedstock as uv
```
- parameter
  - last_business_day : (default = None) True일 시 가장 최근 영업일의 저평가주를 탐색하여 DataFrame으로 반환
  ```python
  undervalued_df_today = uv.find_undervalued_stock(last_business_day=True)
  ```
  - day : (default = None) [str] 입력 시 해당 날짜의 저평가주를 탐색하여 DataFrame으로 반환 (해당 날짜가 주말 및 공휴일일 시 가장 최근 영업일을 기준으로 반환)
  ```python
  undervalued_df = uv.find_undervalued_stock(day='20210101')

### 2. 정형 데이터 칼럼 생성
- 실행 코드 : Quantative_Analysis.ipynb
- 모듈 : QuantativeAnalysis.py
```python
import QuantativeAnalysis as qa
```
- Method
  - dictionary_update : {종목이름 : [시장, 종목코드, WICS산업분류]} 의 형태로 dictionary를 생성하여 pickle 파일로 저장
  ```python
  qa.dictionary_update()
  ```
  - get_stock_price_date : 주가 정보를 칼럼으로 지닌 DataFrame을 생성
    - start_date : [str], 관심 기간의 시작일
    - end_date : [str], 관심 기간의 종료일
    - stock_list : [list, array-like], 관심 종목의 이름들로 이루어진 list 및 array-like 
  ```python
  stock_list = ['삼성전자', 'NAVER', 'GS건설']
  df = qa.get_stock_price_data(start_date='20210101', end_date='20210131', stock_list=stock_list)
  ```
- Class - FinancialStatements : 재무비율 생성
  - path : [str], 재무제표 파일이 있는 디렉토리의 경로 입력
  ```python
  from QuantativeAnalysis import FinancialStatements
  
  fs_instance = FinancialStstements(path='./fsdata/')
  ```
  - Class method
    - get_fsr : 재무제표에서 항목들을 추출하여 재무 비율을 산출하고 종목코드.pickle의 형태로 저장 (pickle파일을 저장할 폴더로 fsr 자동 생성)
    ```python
    fs_instance.get_fsr()
    ```
    - mapping_fsr : 입력받은 DataFrame에 종목 이름, 날짜를 기준으로 재무비율을 join하여 DataFrame의 형태로 반환
    ```python
    fs_df = fs_instance.mapping_fsr(df)
    ```
- Class - TechnicalAnalysis : 기술적 분석에서 사용하는 보조지표 생성
  - df : [DataFrame] 보조지표들을 join할 DataFrame 입력
  ```python
  from QuantativeAnalysis import TechnicalAnalysis
  
  ta_instance = TechnicalAnalysis(df=fs_df)
  ```
  - Class method
    - get_TA : 보조지표들을 산출하여 Class 초기화시 입력된 DataFrame에 종목 이름과 날짜를 기준으로 join
    ```python
    ta_df = ta_instance.get_TA()
    ```
---
## 해당 프로젝트에서 맡은 역할

1. 종토방 게시글 데이터 수집
  - ```./종토방크롤링/종토방 multiprocessing.py``` 작성
  - 파이썬의 ```multiprocessing``` 모듈을 활용하여 종목토론방 크롤링 효율을 향상시킴
2. 종토방 게시글의 긍부정 예측을 위한 LSTM 모델 훈련
 - 데이터 훈련은 라벨링된 12000개의 데이터를 기반으로 함
 - 9천개의 학습 데이터와 1500개의 검증 데이터 및 1500개의 테스트 데이터로 구성됨
 - 레이블은 0과 1로 구성됐으며 각각 부정과 긍정을 뜻함
 - 긍정도란 레이블이 1(긍정)일 확률을 뜻함
3. LSTM 모델 활용 및 변수 창출
  - ```./종토방크롤링/종토방_데이터셋제작.ipynb``` 작성
4. 시각화
  - ```./DART_web/``` 내의 ```static/js/canvasjs-stock-1.2.17``` 제외하고 모두 작성
  - 주가차트는 자바스크립트 상용 라이브러리인 ```CanvasJs```의 ```StockChart()```클래스를 활용하였음
  - 그 외 모든 차트는 오픈소스 라이브러리인 ```Chart.js```의 ```Chart()``` 클래스를 활용함
  - 레포지토리에서는 로컬에서도 시각화 할 수 있게 모든 데이터를 브라우저에 적재함  
  - 그러나 실제로 서버에서 호스팅 할 때에는 브라우저에서 ajax로 요청하는 종목에 대한 데이터만 flask를 활용하여 json 형태로 보내고 변경된 내용을 jQuery로 업데이트함
