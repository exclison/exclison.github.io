---
title: xlsx-style使用(导出表格及修改单元格样式)
date: 2022-11-16 16:50:49
tags: 问题记录
---
# xlsx-style使用(导出表格及修改单元格样式)
针对导出表格
需要与xlsx配合使用,通过xlsx的方法生成sheet对象然后在对象上修改样式


本文对xlsx-style 如何修改单元格样式做了一个大致对介绍,其中前半段对常用的一些属性对官方文档做了一个简单的翻译,希望可以耐心看完,在看后边代码就都懂了
# 安装
```
npm install xlsx-style --save
```
vue安装后导入可能会报错 Can‘t resolve ‘./cptable‘ in ‘xxx\node_modules_xlsx
这里附一个解决方案[xlsx-style报错](https://blog.csdn.net/qq_36512842/article/details/116012393)

# 编写工作簿(Writing Workbooks)
```javascript
/* 文件类型 bookType 可以是 'xlsx' or 'xlsm' or 'xlsb' */
var wopts = { bookType:'xlsx', bookSST:false, type:'binary' };
 
var wbout = XLSX.write(workbook,wopts);
 
function s2ab(s) {
  var buf = new ArrayBuffer(s.length);
  var view = new Uint8Array(buf);
  for (var i=0; i!=s.length; ++i) view[i] = s.charCodeAt(i) & 0xFF;
  return buf;
}
 
/* the saveAs call downloads a file on the local machine */
saveAs(new Blob([s2ab(wbout)],{type:""}), "test.xlsx")
```

# 单元格对象(Cell Object)
可以使用单元格对象来实现对单元格样式对修改,最终导出是一定要使用xlsx-style的方法导出


| key | 简介|
|---|---|
|v|原始值（有关更多信息，请参见“数据类型”部分）|
|w|格式化文本（如果适用)|
|t|单元格类型：b布尔值，n数字，e错误，s字符串，d日期
|f|单元格公式（如果适用）|
|r|富文本编码（如果适用）|
|h|富文本格式的HTML呈现（如果适用）|
|c|与单元格相关的评论**|
|z|与单元格关联的数字格式字符串（如果要求）|
|l|单元超链接对象（.Target包含链接，.tooltip为工具提示）|
|s|单元格的样式/主题（如果适用）

```javascript
//利用xlsxjs获取sheet对象,datalist为格式化好的导出的数据
var ws = XLSX.utils.json_to_sheet(dataList)
      ws['!cols'] = colsconfig //设置列属性,这里后面会讲到,可以修改列的相关属性,例如宽度
      Object.keys(ws).forEach((key) => {//这里遍历单元格给单元格对象设置属性,s为控制样式的属性
        if (key.indexOf('!') < 0) {
          ws[key].s = {
            alignment: {//对齐方式
              horizontal: 'center',//水平居中
              vertical: 'center',//竖直居中
              wrapText: true,//自动换行
            },
          }
        }
      })
```

# 单元格样式(Cell Styles)
单元格样式 即上边代码中的ws[key].s对象下边对应的属性,像alignment对应对齐方式,其他属性查看[官方文档](https://www.npmjs.com/package/xlsx-style)即可,这里不再赘述(官方文档搜索cell styles 直达)

# 工作表对象(Worksheet Object)
#### 这里只介绍几个常用的工作表键,其余请参阅[官方文档](https://www.npmjs.com/package/xlsx-style)中 Worksheet Object 相关介绍

每个不以！开头的键！映射到单元格（使用A-1表示法）
worksheet [address]返回指定地址的单元格对象 (这就是上边代码中获取单元格对象的方法)
特殊的工作表键（可作为worksheet[key]访问，每个键都以！开头）：

- ws['!ref']:基于A-1的范围代表工作表范围。与图纸一起使用的功能应使用此参数来确定范围。分配超出范围的单元格将不被处理。特别是，当手动编写工作表时，请确保更新范围。有关更长的讨论，请参见http://git.io/KIaNKQ
- ws['!cols']:列属性对象的数组。列宽实际上是以规范化的方式存储在文件中的，以“最大位数宽度”（呈示的数字0-9的最大宽度，以像素为单位）衡量。解析后，列对象将像素宽度存储在wpx字段中，将字符宽度存储在wch字段中，并将最大数字宽度存储在MDW字段中。(<font color = #0089FF size=4 face="STCAIYUN">注意:这里的wpx字段与wch字段即为修改列宽度的属性</font>)
```javascript
//结合上文代码中的ws['!cols'] = colsconfig来看
//这里即colsconfig定义的地方,其中wch为列宽度单位,一个字符为两个字节,20则为10个字符宽度
//设置列宽度[{wch:20}]
      const colsconfig = []
      Object.keys(keyMap).forEach(key=>{
        colsconfig.push({
          wch:20
        })
      })
      ws['!cols'] = colsconfig
```
- ws['!merges']:与工作表中合并单元格相对应的范围对象数组。明文实用程序不知道合并单元格。CSV导出将写入合并范围内的所有单元格（如果存在），因此请确保仅设置范围内的第一个单元格（左上角）。
```javascript
//合并单元格 这里指定列合并单元格的范围,与列宽度设置类似也是一个数组
 	let excelMerges = []
	 excelMerges.push(
		{
 	     s: {r: 0, c: 0},
  		 e: {r: 0, c: 2}
  		}
	);
	ws['!merges'] = excelMerges
```
其中push进去的对象的具体内容请参考[官方文档](https://www.npmjs.com/package/xlsx-style)中 General Structures相关介绍

##### 附上完整代码,因为是项目中的代码,所以做了简化,大致理解流程就可以

```bash
import XLSX from 'xlsx'
import XLSXStyle from 'xlsx-style'
import XLSX_SAVE from 'file-saver'
```

```javascript

      //设置列宽度[{wch:20}]
      const colsconfig = []
      Object.keys(keyMap).forEach(key=>{
        colsconfig.push({
          wch:20
        })
      })
      let arrangeInfo = this.getArrangeInfo
      
      //组装数据 这是我的数据解析过程,自己的用自己的就好
      const dataList = arrangeInfo.map((order) => {
        return Object.keys(keyMap).reduce((total, key) => {
          const title = getTitleList(key).join('\n')
          // key = key1,key2,key3...

          // keyList 表头对应的字段数组
          const keyList = keyMap[key]
          const cell = keyList.reduce((v, c) => {
            // v 汇总字符串 c 数据字段
            let val = order[c] || ''
            if (fieldMathod[c]) {
                val = fieldMathod[c](val) || ''
            }
            v += '\n' + val
            return v.trim() //去除前后空字符串
          }, '')

          total[title] = cell
          return total
        }, {})
      })

      //设置excel属性
      var wb = {
        SheetNames: ['导出表格'],
        Sheets: {},
        Props: {},
      }
      var ws = XLSX.utils.json_to_sheet(dataList)
      ws['!cols'] = colsconfig //设置列属性
      Object.keys(ws).forEach((key) => {//设置单元格属性
        if (key.indexOf('!') < 0) {
          ws[key].s = {
            alignment: {
              horizontal: 'center',
              vertical: 'center',
              wrapText: true,
            },
          }
        }
      })
      // ws["!merges"] = excelMerges;
      wb.Sheets['导出表格'] = ws
      const wbout = XLSXStyle.write(wb, {
        type: 'binary',
        bookType: 'xlsx',
      })
      XLSX_SAVE.saveAs(
        new Blob([this.s2ab(wbout)], {
          type: 'application/octet-stream',
        }),
        '导出表格.xlsx'
      )
    },
```