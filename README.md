# (작성중)

## 1️⃣분석목적

- 게임 데이터로 현황 파악
- 추후에 게임 출시 전략 수립을 위한 인사이트 도출

## 2️⃣분석방법

- EDA
- Data Wrangling
- Kruskal-Waliis test


## 3️⃣분석순서

- **데이터파악**
    ![image](https://user-images.githubusercontent.com/23291338/210584485-30478818-5a80-4f0a-8d98-36065f47a844.png)
    
    - Year는 실수형이 아닌 정수형으로 변경해준다.
    - Year의 경우 nan값이 271개로 결측치 처리 후에 타입 변환.
    - Genre, Publisher의 결측치 처리
    - NA_Sales, EU_Sales, JP_Sales, Other_Sales 은 object가 아닌 float으로 데이터 타입 변경.데이터에 문자열이 없는지 체크하고 타입변환.
    
- **데이터전처리**
    - `Genre` `Year` `Publisher`
        - 결측치 비율 - 각각 0.3%, 16%, 0.3%
        - genre, publisher는 결측치 비율이 낮아서 drop하지만, year의 경우 16%이다.
        - Year의 평균값이나 최빈값을 넣거나, 그룹을 지어서 그룹의 평균값을 넣으려고 했지만, 어떤 그룹으로 묶냐에 따라서도 결과가 달라지기 때문에 과감히 drop
        - Year : 연도는 실수형이 아닌 정수형으로 변경. 그리고 2자리에서 4자리로 변경.
            
        ```python
        # 연도 2자리에서 4자리로 바꾸기
        for i in range(20):
          df.loc[df['Year'] == i, 'Year'] = 2000+i
        df.loc[df['Year'] < 100, 'Year'] += 1900
        ```
            
    
    - `NA_sales` `EU_Sales` `JP_Sales` `Other_Sales`
        - 각 지역의 출고량 단위 맞추기
        
        ```python
        # 출고량 컬럼들 단위 맞춰주기
        df[df['NA_Sales'].str.contains(r"[a-zA-Z]")==True]
        
        # 단위가 k인 경우 정수형이고, M인 경우 실수형으로 표현되어있따. 
        # M인 경우는 단위를 없애주고, k인경우 0.001을 곱해 단위 맞춰준다.
        df['NA_Sales'] = df['NA_Sales'].replace({'[kK]': '*0.001', '[mM]': ''}, regex=True).map(pd.eval).astype(float)
        df['EU_Sales'] = df['EU_Sales'].replace({'[kK]': '*0.001', '[mM]': ''}, regex=True).map(pd.eval).astype(float)
        df['JP_Sales'] = df['JP_Sales'].replace({'[kK]': '*0.001', '[mM]': ''}, regex=True).map(pd.eval).astype(float)
        df['Other_Sales'] = df['Other_Sales'].replace({'[kK]': '*0.001', '[mM]': ''}, regex=True).map(pd.eval).astype(float)
        ```
        
    - 중복값 확인
      ```
      # 중복값
      df.duplicated().sum()
      ```
- **EDA, Data Wrangling , 가설검정**
    - Sales total 컬럼 추가
    ```
    df['Sales_total'] = df['NA_Sales'] + df['EU_Sales'] + df['JP_Sales'] + df['Other_Sales']
    ```
    ![image](https://user-images.githubusercontent.com/23291338/210585560-660ea8da-400f-4a68-b48d-4d8d06b46098.png)



## 4️⃣결론

- take two interactive회사에서 액션 장르의 게임을 북미로 출시하고, 지원되는 플랫폼은 PS3로 선정합니다. 추가 지역별로 플랫폼 선호도를 봤을때 북미지역은 PS3보다 PS2의 선호도가 높은 것을 확인할 수 있었지만, 일부분 호환이 가능하기때문에 PS3로 선정해도 무방하다고 판단.

## 5️⃣회고⭐
