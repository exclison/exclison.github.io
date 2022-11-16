---
title: 使用xlsxjs实现上传表格并解析数据
date: 2022-11-16 17:09:47
tags: xlsxjs
---

# 关于 XLSXJS 使用记录

## 使用 xlsx.js + vue + iview 实现上传表格

1. 先使用 upload 组件实现上传文件,可用其他方式替代
2. 拿到文件流之后就可以用 FileReader 读取文件了
3. 读取文件之后使用 xlsx 将数据读取为 binary 格式
4. 将文件使用 xlsx.utils.sheet_to_json 读取为 json 格式
5. 然后就可以根据自己需要解析数据了

上代码

```javascript
// 处理文件上传
    handleUpload(file) { //这里为vue上传文件时拿到文件流的回调函数
      if (!this.validateFormat(file.name)) return false // 验证格式

      // 初始化状态
      this.loadingStatus = true
      this.$emit('startImport')

      var reader = new FileReader() //创建FileReader实例

      const $t = this //映射this

      reader.onload = function(e) {
        var data = e.target.result

        //使用xlsx读取文件
        if ($t.RAS) {
          $t.wb = XLSX.read(data, {
            type: 'binary',
          })
        } else {
          $t.wb = XLSX.read($t.fixdata(data), {
            type: 'binary',
          })
        }

        // 读取每个sheet的数据
        const jsons = []
        for (const sheetName of $t.wb.SheetNames) {
          //解析成json数据
          // const json = XLSX.utils.sheet_to_json($t.wb.Sheets[sheetName])
          const json = XLSX.utils.sheet_to_json($t.wb.Sheets[sheetName], {
            header: 'A',
            raw:true
          })
          jsons.push(json)
        }



        // 处理读取后的json数据
        const objData = $t.analyzeData(jsons)

        // 结束状态
        $t.loadingStatus = false

        $t.$emit('finishImport', objData)
      }
      //读取文件
      if (this.RAS) reader.readAsBinaryString(file)
      else reader.readAsArrayBuffer(file)

      return false
    },
```

## 关于 xlsx.utils.sheet_to_json 的参数

```javascript
    /** Converts a worksheet object to an array of JSON objects */
    sheet_to_json<T>(worksheet: WorkSheet, opts?: Sheet2JSONOpts): T[];
    sheet_to_json(worksheet: WorkSheet, opts?: Sheet2JSONOpts): any[][];
    sheet_to_json(worksheet: WorkSheet, opts?: Sheet2JSONOpts): any[];
```

1. 第一个参数为需要解析的表格 sheet 数据

2. 第二个参数为各种选项,是一个对象

   若没有第二个参数,并且表格中没有合并单元格

   获取的数据将是这样的

   ```javascript
   [
     [
       {
         表头1: "值1",
         表头2: "值2",
         表头3: "值3",
         表头4: "值4",
         表头5: "值5",
       },
       {
         表头1: "值1",
         表头2: "值2",
         表头3: "值3",
         表头4: "值4",
         表头5: "值5",
       },
     ],
   ];
   ```

   - header

     该参数可以设置表头,类型可以是数字,字符串,数组

     目前只知道可以传 1、'A'、[...]

     ```javascript
     const json = XLSX.utils.sheet_to_json($t.wb.Sheets[sheetName], { header: "A" })[
       //json数据将是这样的 有合并单元格将会不同,自己合理调整就可以
       [
         {
           A: "表头1",
           B: "表头1",
           C: "表头1",
           D: "表头1",
           E: "表头1",
           F: "表头1",
         },
         {
           A: "值1",
           B: "值1",
           C: "值1",
           D: "值1",
           E: "值1",
           F: "值1",
         },
       ]
     ];

     const json = XLSX.utils.sheet_to_json($t.wb.Sheets[sheetName], { header: 1 });

     const json = XLSX.utils.sheet_to_json($t.wb.Sheets[sheetName], {
       header: ["num", "name", "tel", "address"],
     });
     ```

   - raw

   - Range

     range 指的是解析范围,并没有深入研究
