---
title: between 運算子 - Azure 資料總管
description: 本文說明 Azure 資料總管中的 between 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.localizationpriority: high
ms.openlocfilehash: 8bb2049c7bc7c81092eb137c820f650bf88abc4e
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512973"
---
# <a name="between-operator"></a>between 運算子

比對內含範圍內的輸入。

```kusto
Table1 | where Num1 between (1 .. 10)
Table1 | where Time between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`between` 可以在任何數值、日期時間或時間範圍運算式上操作。
 
## <a name="syntax"></a>語法

*T* `|` `where` *expr* `between` `(`*leftRange*` .. `*rightRange*`)`   
 
如果 *expr* 運算式為日期時間，則會提供另一個語法糖 (syntactic sugar) 語法：

*T* `|` `where` *expr* `between` `(`*leftRangeDateTime*` .. `*rightRangeTimespan*`)`   

## <a name="arguments"></a>引數

* *T* - 要比對其記錄的表格式輸入。
* *expr* - 要篩選的運算式。
* *leftRange* - 左邊範圍 (含) 的運算式。
* *rightRange* - 右邊範圍 (含) 的運算式。

## <a name="returns"></a>傳回

*T* 中的資料列，其 (*expr* >= *leftRange* 和 *expr* <= *rightRange*) 述詞評估為 `true`。

## <a name="examples"></a>範例  

**使用 'between' 運算子篩選數值**  

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

**使用 'between' 運算子篩選日期時間**  

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
