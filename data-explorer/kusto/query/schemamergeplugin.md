---
title: schema_merge外掛程式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的schema_merge外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 67326a0e7a92d064613ee3a3de2851addb502fc9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509243"
---
# <a name="schema_merge-plugin"></a>schema_merge外掛程式

將表格架構定義合併到統一架構中。 

架構定義應採用[getschema](./getschemaoperator.md)運算元生成的格式。

架構合併操作將出現在輸入架構中的列合併,並嘗試將資料類型減少到常見數據類型(如果數據類型無法減少,則問題列上顯示錯誤)。

```kusto
let Schema1=Table1 | getschema;
let Schema2=Table2 | getschema;
union Schema1, Schema2 | evaluate schema_merge()
```

**語法**

`T``|``evaluate`保留`schema_merge(`訂單*PreserveOrder*`)`

**引數**

* *保留順序*:(可選)`true`當 設置為 時,將定向到外掛程式以驗證第一個表格架構定義的列順序保留。 換句話說,如果同一列出現在多個架構中,則列的表位必須與它出現的第一個架構中相同。 預設值為 `true`。

**傳回**

外掛`schema_merge`程式 將輸出類比返回[到 getschema](./getschemaoperator.md)運算符返回的內容。

**範例**

與附加新欄位的架構合併:

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, HttpStatus:int, Referrer:string)[] | getschema;
union schema1, schema2 | evaluate schema_merge()
```

*結果*

|ColumnName | ColumnOrdinal | DataType | ColumnType|
|---|---|---|---|
|Uri|0|System.String|字串|
|HttpStatus|1|System.Int32|int|
|Referrer|2|System.String|字串|

合併與具有不同欄位的架構(`HttpStatus`從新變數`1`為`2`新變體中的序形變更):

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, Referrer:string, HttpStatus:int)[] | getschema;
union schema1, schema2 | evaluate schema_merge()
```

*結果*

|ColumnName | ColumnOrdinal | DataType | ColumnType|
|---|---|---|---|
|Uri|0|System.String|字串|
|Referrer|1|System.String|字串|
|HttpStatus|-1|錯誤(未知 CSL 類型:錯誤(欄位順序錯誤))|錯誤(欄位順序不排序)|

與具有不同欄位列但`PreserveOrder`設定`false`為此時間的架構合併:

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, Referrer:string, HttpStatus:int)[] | getschema;
union schema1, schema2 | evaluate schema_merge(PreserveOrder = false)
```

*結果*

|ColumnName | ColumnOrdinal | DataType | ColumnType|
|---|---|---|---|
|Uri|0|System.String|字串
|Referrer|1|System.String|字串
|HttpStatus|2|System.Int32|int|