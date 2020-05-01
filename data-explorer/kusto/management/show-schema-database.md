---
title: 。顯示資料庫架構-Azure 資料總管 |Microsoft Docs
description: 本文說明如何在 Azure 資料總管中顯示資料庫架構。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 90a48ec3a830e754bf823712ca7016d162474a39
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616978"
---
# <a name="show-databases-schema"></a>。顯示資料庫架構

以單一資料表或 JSON 物件中的所有資料表和資料行，傳回所選資料庫結構的一般清單。
與版本搭配使用時，只有在版本比提供的版本更新時，才會傳回資料庫。

> [!NOTE]
> 版本應僅以 "vMM.mm" 格式提供。 MM 代表主要版本，而 mm 代表次要版本。

`.show``database` *DatabaseName* DatabaseName `schema` [`details`] [`if_later_than` *版本*]] 

`.show``database` *DatabaseName* DatabaseName `schema` [`if_later_than` *"Version"*] `as``json`
 
`.show``databases` `,` *DatabaseName1* ... `(``)` `schema` [`details` | `as` `json`]
 
`.show``databases` *"Version"* `,` DatabaseName1 if_later_than 「版本」 ... *DatabaseName1* `(``)` `schema` [`details` | `as` `json`]

**範例** 
 
資料庫 ' TestDB ' 有1個數據表，稱為「事件」。

```kusto
.show database TestDB schema 
```

**範例輸出**

|DatabaseName|TableName|ColumnName|ColumnType|IsDefaultTable|IsDefaultColumn|PrettyName|版本
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v1。1       
|TestDB|事件|||True|False||       
|TestDB|事件| 名稱|System.String|True|False||     
|TestDB|事件| StartTime|  System.DateTime|True|False||    
|TestDB|事件| EndTime|    System.DateTime|True|False||        
|TestDB|事件| City|   System.String|True| False||     
|TestDB|事件| SessionId|  System.Int32|True|  True|| 

**範例** 
 
```kusto
.show database TestDB schema if_later_than "v1.0" 
```
**範例輸出**

|DatabaseName|TableName|ColumnName|ColumnType|IsDefaultTable|IsDefaultColumn|PrettyName|版本
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v1。1       
|TestDB|事件|||True|False||       
|TestDB|事件| 名稱|System.String|True|False||     
|TestDB|事件| StartTime|  System.DateTime|True|False||    
|TestDB|事件| EndTime|    System.DateTime|True|False||        
|TestDB|事件| City|   System.String|True| False||     
|TestDB|事件| SessionId|  System.Int32|True|  True||  

由於提供的版本低於目前的資料庫版本，因此會傳回 ' TestDB ' 架構。 提供相等或更高版本會產生空的結果。

**範例** 
 
```kusto
.show database TestDB schema as json
```

**範例輸出**

```json
"{""Databases"":{""TestDB"":{""Name"":""TestDB"",""Tables"":{""Events"":{""Name"":""Events"",""DefaultColumn"":null,""OrderedColumns"":[{""Name"":""Name"",""Type"":""System.String""},{""Name"":""StartTime"",""Type"":""System.DateTime""},{""Name"":""EndTime"",""Type"":""System.DateTime""},{""Name"":""City"",""Type"":""System.String""},{""Name"":""SessionId"",""Type"":""System.Int32""}]}},""PrettyName"":null,""MajorVersion"":1,""MinorVersion"":1,""Functions"":{}}}}"
```

