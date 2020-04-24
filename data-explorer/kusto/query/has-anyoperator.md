---
title: has_any運算符 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中has_any運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 42a181f7c75ef36119f7f2915fd5327d4a656eb8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514309"
---
# <a name="has_any-operator"></a>has_any 運算子

`has_any`基於提供的一組值的運算符篩選器。

```kusto
Table1 | where col has_any ('value1', 'value2')
```

**語法**

*標量表示式的**T* `|` `where` *col*`has_any``(`清單`)`   
*T* `where` `(`T *col* col*表格表示式*`|``has_any``)`   
 
**引數**

* *T* - 要篩選其記錄的表格輸入。
* *col* - 要篩選的欄。
* *表示式清單*─表格、標量或文字表示式 Comma 分隔清單  
* *表格表示式*─ 具有一組值的表格表示式(如果表示式有多個欄,則使用第一欄)

**傳回**

謂詞為*T*中的行`true`

**注意事項**

* 運算式清單可以生成最多`10,000`的值。    
* 對於表格表達式,將選擇結果集的第一列。   

**範例：**  

**運算子的簡單`has_any`用法:**  

```kusto
StormEvents 
| where State has_any ("CAROLINA", "DAKOTA", "NEW") 
| summarize count() by State
```

|State|count_|
|---|---|
|紐約|1750|
|北卡羅來納州|1721|
|南達科他州|1567|
|新澤西|1044|
|南卡羅來納州|915|
|北達科他州|905|
|新墨西哥州|527|
|新罕布希爾州|394|


**使用動態陣列:**

```kusto
let states = dynamic(['south', 'north']);
StormEvents 
| where State has_any (states)
| summarize count() by State
```

|State|count_|
|---|---|
|北卡羅來納州|1721|
|南達科他州|1567|
|南卡羅來納州|915|
|北達科他州|905|
|大西洋南部|193|
|大西洋北部|188|