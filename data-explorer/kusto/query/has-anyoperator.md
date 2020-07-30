---
title: has_any 運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的 has_any 操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 4485dde5eb77478e5fd75ce388ada7f4232f2ddb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347624"
---
# <a name="has_any-operator"></a>has_any 運算子

`has_any`運算子會根據提供的一組值來篩選。

```kusto
Table1 | where col has_any ('value1', 'value2')
```

## <a name="syntax"></a>語法

*T*純 `|` `where` *col* `has_any` `(` *量運算式的*T 欄清單`)`   
*T* `|` `where` *欄* `has_any` `(` *表格式運算式*`)`   
 
## <a name="arguments"></a>引數

* 要篩選其記錄的*T*表格式輸入。
* 要*篩選的欄-資料*行。
* *運算式清單*-表格式、純量或常值運算式的逗號分隔清單  
* *表格式運算式*-具有一組值的表格式運算式（如果 expression 有多個資料行，則會使用第一個資料行）

## <a name="returns"></a>傳回

述詞為之*T*中的資料列`true`

**備註**

* 運算式清單可產生最多個 `10,000` 值。    
* 若為表格式運算式，則會選取結果集的第一個資料行。   

**範例：**  

**運算子的簡單用法 `has_any` ：**  

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| where State has_any ("CAROLINA", "DAKOTA", "NEW") 
| summarize count() by State
```

|狀態|count_|
|---|---|
|紐約|1750|
|北卡羅萊納州|1721|
|南北達科他|1567|
|新增 JERSEY|1044|
|南卡羅萊納州|915|
|北北達科他|905|
|新增墨西哥|527|
|新增新罕布什爾州|394|


**使用動態陣列：**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let states = dynamic(['south', 'north']);
StormEvents 
| where State has_any (states)
| summarize count() by State
```

|狀態|count_|
|---|---|
|北卡羅萊納州|1721|
|南北達科他|1567|
|南卡羅萊納州|915|
|北北達科他|905|
|大西洋南部|193|
|大西洋北部|188|
