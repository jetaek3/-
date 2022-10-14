# 시각화 코드 정리

---






# 전기차 충전소 시각화 
```c
from cmath import nan
import pandas as pd
import json
from folium.plugins import FastMarkerCluster
import folium

df.read_csv('./echarge_yongin_naver_xy.csv',encoding='utf-8-sig')

temp = df.dropna(subset=['위도'])
yon_lat, yon_lng = 37.2410864, 127.1775537
my_map = folium.Map(location=[yon_lat, yon_lng], zoom_start=5)

for i,row in temp.iterrows():
    folium.Circle(
            location = (row['위도'], row['경도']),
            # tooltip = df.loc[i, '고유번호'],
            radius = 50
        ).add_to(my_map)
# save the map 
my_map.save("save_file2.html")
```

# 인구 수 시각화
```c
state_geo='용인시_읍면동_EPSG4326.geojson'
geo_data=json.load(open(state_geo,encoding='utf-8'))
yongin_car = pd.read_csv('용인시 전기차_인구수_현황_코드.csv')


#지도에 색 적용 및 데이터 연결
m = folium.Map(location = [37.2411,127.1776], zoom_start = 13)
folium.Choropleth(
    geo_data = geo_data,
    data = df,
    columns = ('코드', '인구수'),
    key_on='properties.adm_cd',
    fill_color = 'RdYlGn',
    fill_opacity = 0.7,
    line_opacity = 0.5).add_to(m)

m.save('yongin_population.html')
m
```

# 전기차 등록대수 시각화
```c
m = folium.Map(location = [37.2411,127.1776], zoom_start = 13)

yongin_car = pd.read_csv('용인시 전기차_인구수_현황_코드.csv')
yongin_car


#지도에 색 적용 및 데이터 연결
folium.Choropleth(
    geo_data = geo_data,
    data = df,
    columns = ('코드', '등록 수'),
    key_on='properties.adm_cd',
    fill_color = 'RdYlGn',
    fill_opacity = 0.7,
    line_opacity = 0.5).add_to(m)

m.save('yongin_population.html')
m
```



# cctv시각화
```c
import requests
from bs4 import BeautifulSoup
import json 
import re
import pandas as pd
from tqdm import tqdm
#cctv 위치(위도,경도) 가져오기 --용인교통첨단센터
name=[]
x_pos=[]
y_pos=[]

url='http://its.yongin.go.kr/common/getCctvCtlr.do'
headers={
    
    'Accept': '*/*',
'Accept-Encoding': 'gzip, deflate',
'Accept-Language':  'ko,en;q=0.9,en-US;q=0.8',
'Connection': 'keep-alive',
'Content-Length': '0',
'Host': 'its.yongin.go.kr',
'Origin': 'http://its.yongin.go.kr',
'Referer': 'http://its.yongin.go.kr/traffic/cctv.do',
'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.0.0 Safari/537.36 Edg/106.0.1370.37',
'X-Requested-With': 'XMLHttpRequest'
}

resp=requests.post(url,headers=headers)
jsn=json.loads(resp.text)
for i in range(len(jsn)):
  x_pos.append(jsn[i]['X_CRDN'])
  y_pos.append(jsn[i]['Y_CRDN'])
  name.append(jsn[i]['ISTL_LCTN_NM'])
```

```c
key_=['X_CRDN','Y_CRDN','ISTL_LCTN_NM']
value_=[x_pos,y_pos,name]
total=dict(zip(key_,value_))
total=pd.DataFrame(total)
total.to_csv('용인시 cctv위치정보.csv',encoding='utf-8')

```
```c
#시각화
import folium
map_osm = folium.Map(location=[y_pos[0],x_pos[0]],zoom_start=13)
for i in tqdm(range(len(y_pos))):
    folium.CircleMarker(location=[y_pos[i],x_pos[i]],popup= name[i], radius=3, color="#3186cc", fill_color="#3186cc").add_to(map_osm)

map_osm
```

---









---
# 공영주차장
