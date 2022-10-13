# 코드 정리

---

```c
#cctv시각화
import requests
from bs4 import BeautifulSoup
import json 
import re
import pandas as pd
from tqdm import tqdm

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
import folium
map_osm = folium.Map(location=[y_pos[0],x_pos[0]],zoom_start=13)
for i in tqdm(range(len(y_pos))):
    folium.CircleMarker(location=[y_pos[i],x_pos[i]],popup= name[i], radius=3, color="#3186cc", fill_color="#3186cc").add_to(map_osm)

map_osm
```

---

