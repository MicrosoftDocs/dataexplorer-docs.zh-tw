---
title: as 運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 as 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f9d7a60b3c39fb0b7357c2bbe68533252f794347
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349477"
---
# <a name="as-operator"></a>as 運算子

將名稱系結至運算子的輸入表格式運算式，讓查詢可以多次參考表格式運算式的值，而不會破壞查詢並透過[let 語句](letstatement.md)系結名稱。

## <a name="syntax"></a>語法

*T* `|` `as` [ `hint.materialized` `=` `true` ]*名稱*

## <a name="arguments"></a>引數

* *T*：表格式運算式。
* *名稱*：表格式運算式的暫存名稱。
* `hint.materialized`：如果設定為 `true` ，則會具體化表格式運算式的值，如同[具體化（）](./materializefunction.md)函式呼叫所包裝的。

**備註**

* 提供的名稱 `as` 將用於 union 的資料 `withsource=` 行、尋找[union](./unionoperator.md)的資料行 `source_` ，以及[find](./findoperator.md) `$table` [搜尋](./searchoperator.md)的資料行。

* 使用[聯結](./joinoperator.md)的外部表格式輸入（）中的運算子所命名的表格式運算式， `$left` 也可以用於聯結的表格式內部輸入（ `$right` ）。

## <a name="examples"></a>範例

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