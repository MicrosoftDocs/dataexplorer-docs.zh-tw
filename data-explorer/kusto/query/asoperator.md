---
title: 作為運算子 - Azure 資料資源管理員 |微軟文件
description: 本文在 Azure 資料資源管理器中描述為運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 05dc96fb7eec773d1e55d8b94a33cdda928622ff
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518423"
---
# <a name="as-operator"></a>as 運算子

將名稱綁定到運算符的輸入表格表示式,從而使查詢多次引用表格表達式的值,而不會破壞查詢並通過[let 語句](letstatement.md)綁定名稱。

**語法**

*T* `as` `=` T =*Name*名稱`true``|``hint.materialized`

**引數**

* *T*: 表格表示式。
* *名稱*:表表表達式的臨時名稱。
* `hint.materialized`:如果設置為`true`,則表位表達式的值將具體化,就像它被[具體()函數](./materializefunction.md)調用包裹一樣。

**注意事項**

* 給出`as`的名稱將用於`withsource=`[聯合](./unionoperator.md)列、`source_`[尋找](./findoperator.md)列和`$table`[搜尋](./searchoperator.md)欄位。

* 在[聯接](./joinoperator.md)的外部表格輸入 ()`$left`中使用運算符命名的表格表達式也可用於聯接的表格內部輸入 ()。`$right`

**範例**

```kusto
// 1. In the following 2 example the union's generated TableName column will consist of 'T1' and 'T2'
range x from 1 to 10 step 1 
| as T1 
| union withsource=TableName T2

union withsource=TableName (range x from 1 to 10 step 1 | as T1), T2

// 2. In the following example, the 'left side' of the join will be: 
//      MyLogTable filtered by type == "Event" and Name == "Start"
//    and the 'right side' of the join will be: 
//      MyLogTable filtered by type == "Event" and Name == "Stop"
MyLogTable  
| where type == "Event"
| as T
| where Name == "Start"
| join (
    T
    | where Name == "Stop"
) on ActivityId
```