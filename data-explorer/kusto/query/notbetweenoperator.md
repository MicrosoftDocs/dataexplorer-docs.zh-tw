---
title: 不在運算元之間 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的運算符之間的不。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: eacde679f05ff79f5ee0d223ba005217dbf5192c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512082"
---
# <a name="between-operator"></a>!在操作員之間

匹配超出包含範圍的輸入。

```kusto
Table1 | where Num1 !between (1 .. 10)
Table1 | where Time !between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`!between`可以對任何數位、日期時間或時間跨度運算式進行操作。
 
**語法**

*T* `where` *rightRange*`(` *expr*`!between`T*leftRange*expr` .. `左範圍 右範圍`|``)`   
 
如果*expr*表示日期時間 - 提供另一種語法糖語法:

*T* `where` *rightRangeTimespan*`(` *expr*`!between`T*leftRangeDateTime*expr` .. `左範圍 時間 右範圍時間跨度`|``)`   

**引數**

* *T* - 要匹配其記錄的表格輸入。
* *expr* - 要篩選的運算式。
* *左範圍*- 左側範圍的運算式(包括)。
* *右範圍*- rihgt 範圍(包括)的運算式。

**傳回**

*T*中的行,謂詞(*左* < *範圍*或*expr* > *右範圍*) 計算`true`為的行。

**範例**  

**使用「!"運算符篩選數值**  

```kusto
range x from 1 to 10 step 1
| where x !between (5 .. 9)
```

|x|
|---|
|1|
|2|
|3|
|4|
|10|

**使用「介於「運算符篩選日期時間**  


```kusto
StormEvents
| where StartTime !between (datetime(2007-07-27) .. datetime(2007-07-30))
| count 
```

|Count|
|---|
|58590|


```kusto
StormEvents
| where StartTime !between (datetime(2007-07-27) .. 3d)
| count 
```

|Count|
|---|
|58590|