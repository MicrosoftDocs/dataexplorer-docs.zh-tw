---
title: has_any 操作員-Azure 資料總管
description: 本文說明 Azure 資料總管中的 has_any 操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 012a6b0555778a30055ac9d7f4619c7b74d13988
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241627"
---
# <a name="has_any-operator"></a>has_any 運算子

`has_any` 運算子會根據提供的一組值進行篩選。

```kusto
Table1 | where col has_any ('value1', 'value2')
```

## <a name="syntax"></a>語法

*T*純 `|` `where` *col* `has_any` `(` *量運算式的*T 欄清單`)`   
*T* `|` `where` *col* `has_any` `(` *表格式運算式*`)`   
 
## <a name="arguments"></a>引數

* 要篩選其記錄的*T*表格式輸入。
* 要*篩選的資料*行欄。
* *運算式清單* -表格式、純量或常值運算式的逗點分隔清單  
* *表格式運算式* -表格式運算式，其中包含一組值 (如果運算式有多個資料行，則會使用第一個資料行) 

## <a name="returns"></a>傳回

述詞為 *T* 中的資料列 `true`

**備註**

* 運算式清單可以產生最多 `10,000` 值。    
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
|北卡羅來納州|1721|
|南達科他州|1567|
|新澤西|1044|
|南卡羅來納州|915|
|北達科他州|905|
|新墨西哥|527|
|新罕布什爾州|394|


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
|北卡羅來納州|1721|
|南達科他州|1567|
|南卡羅來納州|915|
|北達科他州|905|
|大西洋南部|193|
|大西洋北部|188|
