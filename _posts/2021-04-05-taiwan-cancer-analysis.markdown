---
layout: post 
title:  "Taiwan Cancer Analysis"
date:   2021-04-05 22:31:30 +0800 
categories: Data_Science 
tags : Python 
mathjax: true
---
* content
{:toc} 
利用政府開放資料對台灣男女性癌症做關聯分析，
並練習畫雲林縣地圖。

[Github:q8977452/taiwan_cancer_analysis](https://github.com/q8977452/taiwan_cancer_analysis)



  

## 資料來源
* [癌症發生統計](https://data.gov.tw/dataset/6399)
* [癌症死因統計](https://data.gov.tw/dataset/8154)
* [鄉鎮市區界線(TWD97經緯度)](https://data.gov.tw/dataset/7441)
	
## 使用技巧
* 合併資料
    ```python
    pd.merge(left=左邊DataFrame, right=右邊DataFrame, 
          left_on=None, right_on=None)
      xxxx_on: 可以是列名、索引級名稱
      how: 
          inner: 交集(default)
          outer: 聯集
    
    ```
* 畫地圖
   ```python
   import geopandas as gpd
   town_shp = gpd.read_file('../data/map/TOWN_MOI_1091016.shp',encoding='utf-8')
   yunlin_town_shp=town_shp[town_shp['COUNTYNAME']=='雲林縣']
   yunlin_town_shp.plot(cmap='RdBu')
   ```
  
## 108年癌症關聯分析
### 男性
![](https://raw.githubusercontent.com/q8977452/taiwan_cancer_analysis/main/img/male_cancer_cor.png)

### 女性
![](https://raw.githubusercontent.com/q8977452/taiwan_cancer_analysis/main/img/female_cancer_cor.png)

### 雲林縣地圖
![](https://raw.githubusercontent.com/q8977452/taiwan_cancer_analysis/main/img/yulin.png)

## Reference

* [用pip安装GDAL时出错解决办法](https://blog.csdn.net/qq_38316655/article/details/105697886)
* [stackoverflow - Can't install Fiona on Windows](https://stackoverflow.com/questions/50876702/cant-install-fiona-on-windows)
* [详解pandas库的pd.merge函数](https://blog.csdn.net/brucewong0516/article/details/82707492)