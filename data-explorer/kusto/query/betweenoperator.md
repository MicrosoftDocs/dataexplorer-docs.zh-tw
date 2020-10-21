---
title: 介於運算子之間-Azure 資料總管
description: 本文說明 Azure 資料總管中的操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 15112a72c289d87f6a1f1a2b035cb13bad81acdb
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245457"
---
# <a name="between-operator"></a>between 運算子

比對內含範圍內的輸入。

```kusto
Table1 | where Num1 between (1 .. 10)
Table1 | where Time between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`between` 可以在任何數值、datetime 或 timespan 運算式上操作。
 
## <a name="syntax"></a>語法

*T* `|` `where` *運算式* `between` `(` *leftRange* ` .. ` *rightRange*`)`   
 
如果 *expr* 運算式是 datetime，則會提供另一個語法的血糖語法：

*T* `|` `where` *運算式* `between` `(` *leftRangeDateTime* ` .. ` *rightRangeTimespan*`)`   

## <a name="arguments"></a>引數

* *T* -要比對其記錄的表格式輸入。
* *expr* -要篩選的運算式。
* *leftRange* ：左邊範圍 (內含) 的運算式。
* *rightRange* 運算式，右邊的範圍 (包含) 。

## <a name="returns"></a>傳回

*T*中 (*expr*  >=  *leftRange*和*expr*  <= ) *rightRange*的述詞會評估為的資料列 `true` 。

## <a name="examples"></a>範例  

**使用 ' between ' 運算子篩選數值**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 100 step 1
| where x between (50 .. 55)
```

|x|
|---|
|50|
|51|
|52|
|53|
|54|
|55|

**使用 ' between ' 運算子篩選 datetime**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count 
```

|計數|
|---|
|476|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count 
```

|計數|
|---|
|476|
