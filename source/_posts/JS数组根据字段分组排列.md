---
title: JS数组根据字段分组排列
date: 2022-11-16 16:59:45
tags: javascript
---
# JS数组根据字段分组排列

记录一下项目中用到的技术,不多说,直接上代码

```javascript
/*
     *@name:编排数据
     *@description:对数据根据相应字段分组排列
     *@author: Exclison
     *@date: 2020-11-20 14:58:47
     *@params {array} data: 需要分组的数组
     *@params {string} key: 分组根据的字段
    */
    const orchesData = (data,key)=>{

       const groupBy = (list,fn)=>{
         const groups = {}
         list.forEach(i=>{
          const groupName = fn(i)
          groups[groupName] = groups[groupName] || []
          groups[groupName].push(i)
         })

         const groupList = Object.keys(groups).reduce((a,c)=>{
           return a.concat(groups[c])
         },[])
         return groupList
       }
       const sortList = groupBy(data,(item)=>{
         return item[key]
       })
       return sortList
    },
    
   const list = [
        {"name": "John", "Average": 15, "High": 10, "DtmStamp": 1358226000000},
        {"name": "Jane", "Average": 16, "High": 92, "DtmStamp": 1358226000000},
        {"name": "Jane", "Average": 17, "High": 45, "DtmStamp": 1358226000000},
        {"name": "John", "Average": 18, "High": 87, "DtmStamp": 1358226000000},
        {"name": "Jane", "Average": 15, "High": 10, "DtmStamp": 1358226060000},
        {"name": "John", "Average": 16, "High": 87, "DtmStamp": 1358226060000},
        {"name": "John", "Average": 17, "High": 45, "DtmStamp": 1358226060000},
        {"name": "Jane", "Average": 18, "High": 92, "DtmStamp": 1358226060000}
        ]
   const groupList = orchesData(list,'name')
```

简单分享一下思路

1. 先定义一个groupBy函数,包含两个参数,一个是需要分组的数组,一个是回调函数

2. 回调函数用来返回数组单个项目的对应key的值,也就是list中的每一项的name值,要用它来做组名

3. groupBy里边首先定义一个对象(咱们利用对象属性的唯一性来实现分组)

4. 然后对list数据进行遍历,取出每条数据的组名(也就是例子中的name对应的值)

5. 将组名定义为对象groups的属性并对其赋值(由于对象属性的唯一性,所以每次赋值都是对上次赋值的覆盖),为了避免这个问题,我们先将上一次的数据赋值过来,如果没有就赋值为空数组

6. 然后将这条数据push到我们刚定义的数组里就好了(这样每条数据都在这个对象的相应组名下边的数组里)

   得到的数据类似于这样

   ```javascript
   {
     "John":[
       {"name": "John", "Average": 15, "High": 10, "DtmStamp": 1358226000000},
       {"name": "John", "Average": 18, "High": 87, "DtmStamp": 1358226000000},
       {"name": "John", "Average": 16, "High": 87, "DtmStamp": 1358226060000},
       {"name": "John", "Average": 17, "High": 45, "DtmStamp": 1358226060000},
     ],
      "Jane":[
        {"name": "Jane", "Average": 16, "High": 92, "DtmStamp": 1358226000000},
        {"name": "Jane", "Average": 17, "High": 45, "DtmStamp": 1358226000000},
        {"name": "Jane", "Average": 15, "High": 10, "DtmStamp": 1358226060000},
        {"name": "Jane", "Average": 18, "High": 92, "DtmStamp": 1358226060000},
      ]
   }
   ```

   

7. 最后使用array.reduce方法汇总一下就好了