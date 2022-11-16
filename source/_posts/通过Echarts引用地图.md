---
title: 通过Echarts引用地图
date: 2022-11-16 16:58:13
tags: Echarts
---

# Echarts中国地图



## **引入Echarts**



echarts 中默认包含china.js以及中国各省份的js文件



```javascript
// 指定图表的配置项和数据
      let option = {
        tooltip: {},
        geo: {
          // 这个是重点配置区
          map: "china", // 表示中国地图
          roam: false,
          label: {
            normal: {
              show: false, // 是否显示对应地名
              textStyle: {
                fontSize: "7",
                color:'#fff',
              },
            },
            emphasis: {
              textStyle: {
                fontSize: "7",
                color:'#fff',
              },
            },
          },
          itemStyle: {
            normal: {
              areaColor: 'rgba(30, 52, 118, 1)',
              borderColor: "#00E7FF",
            },
            emphasis: {
              areaColor: 'rgba(30, 52, 118, 1)',
              borderColor: "#00E7FF",
              
            },
          },
          zoom:1.2
          // regions: haveData
        },
        series: [],
      };
```



# 各省份地图

## 获取geoJSON

### 1. 通过[地图选择器](http://datav.aliyun.com/tools/atlas/#&lat=30.343372698262712&lng=106.720060693723&zoom=4.5)下载geoJSON数据,或者直接引用里面的地址

```javascript
  const taiwanJson = require('./taiwan')//下载的geoJSON文件
let myChart = this.$echarts.init(this.$refs.taiwan_chart);
        this.$echarts.registerMap('taiwan',taiwanJson);

        // 指定图表的配置项和数据
        let option = {
          tooltip: {},
          geo: {
            // 这个是重点配置区
            map: "taiwan", // 省份
            roam: true,
            center: [122.50, 25.05],
            label: {
              normal: {
                show: false, // 是否显示对应地名
                textStyle: {
                  fontSize: "7",
                  color:'#fff',
                },
              },
              emphasis: {
                textStyle: {
                  fontSize: "7",
                  color:'#fff',
                },
              },
            },
            itemStyle: {
              normal: {
                areaColor: 'rgba(30, 52, 118, 1)',
                borderColor: "#00E7FF",
              },
              emphasis: {
                areaColor: 'rgba(30, 52, 118, 1)',
                borderColor: "#00E7FF",
              },
            },
            zoom:2
            // regions: haveData
          },
          series: [
            {
              name: "",
              type: "map",
              // coordinateSystem: "geo", // 对应上方配置
              data:[],
            },
          ],
        };

        // 使用刚指定的配置项和数据显示图表。
        myChart.setOption(option);
```


