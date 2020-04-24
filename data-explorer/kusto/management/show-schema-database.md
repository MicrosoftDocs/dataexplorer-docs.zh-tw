---
title: .顯示資料庫架構 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 .顯示 Azure 數據資源管理器中的資料庫架構。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 212aaf428e03190226a17509c97e9acd1394d2b1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519800"
---
# <a name="show-databases-schema"></a>.顯示資料庫架構

返回選取資料庫結構的平面清單,該清單及其所有表和列位於單個表或 JSON 物件中。
當與版本一起使用時,僅當資料庫的版本晚於提供的版本時,才會返回資料庫。

> [!NOTE]
> 該版本應僅以「vMM.mm」格式提供。 MM 表示主要版本,mm 表示次要版本。

`.show``database`*資料庫*`schema`名稱`details``if_later_than` [ ] [*版本"*| 

`.show``database`*資料庫*`schema`名稱`if_later_than`= *"版本"*| `as``json`
 
`.show`資料庫名稱1 *DatabaseName1* ... `(` `databases` `,``)` `schema` [`details` | `as` `json`]
 
`.show``databases``,`資料庫名稱1 if_later_than版本版本...... `(` * * * *`)` `schema` [`details` | `as` `json`]

**範例** 
 
資料庫"TestDB"有 1 個表稱為"事件"。

```
.show database TestDB schema 
```

**範例輸出**

|DatabaseName|TableName|ColumnName|ColumnType|預設表|預設欄|漂亮名稱|版本
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v.1.1       
|TestDB|事件|||True|False||       
|TestDB|事件| 名稱|System.String|True|False||     
|TestDB|事件| StartTime|  System.DateTime|True|False||    
|TestDB|事件| EndTime|    System.DateTime|True|False||        
|TestDB|事件| City|   System.String|True| False||     
|TestDB|事件| SessionId|  System.Int32|True|  True|| 

**範例** 
 
```
.show database TestDB schema if_later_than "v1.0" 
```
**範例輸出**

|DatabaseName|TableName|ColumnName|ColumnType|預設表|預設欄|漂亮名稱|版本
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v.1.1       
|TestDB|事件|||True|False||       
|TestDB|事件| 名稱|System.String|True|False||     
|TestDB|事件| StartTime|  System.DateTime|True|False||    
|TestDB|事件| EndTime|    System.DateTime|True|False||        
|TestDB|事件| City|   System.String|True| False||     
|TestDB|事件| SessionId|  System.Int32|True|  True||  

由於提供的版本低於當前資料庫版本,因此返回了"TestDB"架構。 提供相等或更高的版本將生成空結果。

**範例** 
 
```
.show database TestDB schema as json
```

**範例輸出**

```json
"{""Databases"":{""TestDB"":{""Name"":""TestDB"",""Tables"":{""Events"":{""Name"":""Events"",""DefaultColumn"":null,""OrderedColumns"":[{""Name"":""Name"",""Type"":""System.String""},{""Name"":""StartTime"",""Type"":""System.DateTime""},{""Name"":""EndTime"",""Type"":""System.DateTime""},{""Name"":""City"",""Type"":""System.String""},{""Name"":""SessionId"",""Type"":""System.Int32""}]}},""PrettyName"":null,""MajorVersion"":1,""MinorVersion"":1,""Functions"":{}}}}"
```

