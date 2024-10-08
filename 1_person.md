```python
import pandas as pd
```


```python
import os
csv_file = os.listdir("./origin_csv")
csv_file
```




    ['master2019_01.csv',
     'master2019_02.csv',
     'master2019_03.csv',
     'master2019_04.csv',
     'master2020_01.csv',
     'master2020_02.csv',
     'master2020_03.csv',
     'master2020_04.csv',
     'master2021_01.csv',
     'master2021_02.csv',
     'master2021_03.csv',
     'master2021_04.csv',
     'master2022_01.csv',
     'master2022_02.csv',
     'master2022_03.csv',
     'master2022_04.csv',
     'master2023_01.csv',
     'master2023_02.csv',
     'master2023_03.csv',
     'master2023_04.csv']




```python
# 파일 오픈시 몇개의 행에 좀 더 많은 항목이 들어감 -> 오류 발생
#이를 해결하기 위해서 csv -> list -> dataframe 형태로 바꿈 
# 나중에 줄 없앨 예정

#과정1 , 2019~2023년까지 5년의 1인 가구 데이터를 뽑을 예정 // 
#필요한 컬럼은 product, wide, middle, detail, purchase, family_num_level, age1_group, income_v2


import csv
import pandas as pd

for file_name in range (len(csv_list)):
    file_path = f"./origin_csv/{csv_file[file_name]}"
    f = open(file_path,'rt', encoding = 'utf8')
    reader = csv.reader(f)

    #once contents are available, I then put them in a list
    csv_list = []
    for l in reader:
        csv_list.append(l)
    f.close()

    #now pandas has no problem getting into a df
    df = pd.DataFrame(csv_list)
    
    #첫번째 행을 컬럼으로 지정
    df = df.rename(columns=df.iloc[0])
    
    #필요한 컬럼만 추출한다. 
    df_to_save = df.loc[:,['product', 'wide', 'middle', 'detail', 'purchase', 'family_num_level', 'age1_group', 'income_v2']]
    # 1인 가구 추출
    df_to_save = df_to_save.loc[df_to_save['family_num_level']=='1',:]
    
    
    #purchase 부분에 정수형이 아닌 다른 문자가 들어있는 경우가 있어서 오류남 -> 이를 해결
    df_to_save = df_to_save[pd.to_numeric(df_to_save['purchase'], errors='coerce').notnull()]
    
    #string 형태로 저장되어있는 정수형들을 다시 정수형으로 타입변환 
    df_to_save = df_to_save.astype({'purchase' : 'int', 'family_num_level' :'int', 'age1_group':'int'})
    #csv 파일로 저장 
    df_to_save.to_csv(f'./one_person/one_person_{csv_file[file_name]}')
    

```


    ---------------------------------------------------------------------------

    IndexError                                Traceback (most recent call last)

    Cell In[41], line 13
         10 import pandas as pd
         12 for file_name in range (len(csv_list)):
    ---> 13     file_path = f"./origin_csv/{csv_file[file_name]}"
         14     f = open(file_path,'rt', encoding = 'utf8')
         15     reader = csv.reader(f)
    

    IndexError: list index out of range



```python
# 1인 가구 dataframe을 하나로 합칠 예정 


one_person_list = os.listdir('./one_person')
one_person_list
```




    ['one_person_master2019_01.csv',
     'one_person_master2019_02.csv',
     'one_person_master2019_03.csv',
     'one_person_master2019_04.csv',
     'one_person_master2020_01.csv',
     'one_person_master2020_02.csv',
     'one_person_master2020_03.csv',
     'one_person_master2020_04.csv',
     'one_person_master2021_01.csv',
     'one_person_master2021_02.csv',
     'one_person_master2021_03.csv',
     'one_person_master2021_04.csv',
     'one_person_master2022_01.csv',
     'one_person_master2022_02.csv',
     'one_person_master2022_03.csv',
     'one_person_master2022_04.csv',
     'one_person_master2023_01.csv',
     'one_person_master2023_02.csv',
     'one_person_master2023_03.csv',
     'one_person_master2023_04.csv']




```python

df = pd.DataFrame()

for i in range (len(one_person_list)):
    df2=pd.read_csv(f'./one_person/{one_person_list[i]}')
    df = pd.concat([df, df2], ignore_index=True)
    
    print(one_person_list[i])

df.to_csv('./result/one_person_last_data.csv')
df.to_excel('./result/one_person_last_data.xlsx')
```

    one_person_master2019_01.csv
    one_person_master2019_02.csv
    one_person_master2019_03.csv
    one_person_master2019_04.csv
    one_person_master2020_01.csv
    one_person_master2020_02.csv
    one_person_master2020_03.csv
    one_person_master2020_04.csv
    one_person_master2021_01.csv
    one_person_master2021_02.csv
    one_person_master2021_03.csv
    one_person_master2021_04.csv
    one_person_master2022_01.csv
    one_person_master2022_02.csv
    one_person_master2022_03.csv
    one_person_master2022_04.csv
    one_person_master2023_01.csv
    one_person_master2023_02.csv
    one_person_master2023_03.csv
    one_person_master2023_04.csv
    


```python
print ('다음은 대분류의 갯수입니다.')
df_wide = df['wide'].value_counts()
df_wide.to_csv('./result/one_person_data_wide.csv',  encoding ='utf8')
df_wide.to_excel('./result/one_person_data_wide.xlsx')
print(df_wide)
```

    다음은 대분류의 갯수입니다.
    wide
    가공식품      286972
    축산물        22064
    과일류        17879
    과채류        16089
    조미채류       15655
    엽경채류       12578
    수산물         8032
    특작류         6798
    나물류         6183
    근채류         5523
    기타채소류       3575
    서류          3519
    곡물류         3145
    건과_견과류       999
    기타           310
    오류            36
    과일과채혼합         2
    Name: count, dtype: int64
    


```python
print ('다음은 중분류의 갯수입니다.')
df_mid = df['middle'].value_counts()
df_mid.to_csv('./result/one_person_data_middle.csv', encoding ='utf8')
df_mid.to_excel('./result/one_person_data_mid.xlsx')
print(df_mid)
```

    다음은 중분류의 갯수입니다.
    middle
    밀가공식품         75775
    음료            25883
    쌀가공식품         20994
    수산물가공식품       15822
    주류            14480
                  ...  
    과일과채혼합            2
    기타과채류가공식품         2
    기타서류              2
    메론가공식품            1
    기타조미채류가공식품        1
    Name: count, Length: 188, dtype: int64
    


```python
print ('다음은 소분류의 갯수입니다.')
df_detail = df['detail'].value_counts()
df_detail.to_csv('./result/one_person_data_detail.csv', encoding ='utf8')
df_detail.to_excel('./result/one_person_data_detail.xlsx')
print(df_detail)
```

    다음은 소분류의 갯수입니다.
    detail
    빵류            32289
    과자류           23002
    커피음료          12414
    아이스크림         11159
    기타가공식품        11121
                  ...  
    메론가공식품            1
    기타조미채류가공식품        1
    복분자               1
    잣가공식품             1
    기타쌈채소가공식품         1
    Name: count, Length: 464, dtype: int64
    


```python

```
