---
title: 專案操作員 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的項目運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: de13c240180d00b82736a0dd35cb83c08639682f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510926"
---
# <a name="project-operator"></a>專案操作員

選取要納入、重新命名或捨棄的資料行，以及插入新的計算資料行。 

結果中的資料行順序是由引數順序來指定。 結果中僅包含參數中指定的欄。 刪除輸入中的任何其他列。  (另請參閱 `extend`)。

```kusto
T | project cost=price*quantity, price
```

**語法**

*T* `| project` *欄位*[`=` `,`*表示式*】 [ ... ]
  
或
  
*T* `| project` =*欄位* | `(`*名稱*`,``)` `=`[ ]`,` =*運算式*= ...]

**引數**

* *T*: 輸入表。
* *欄位名稱:* 要顯示在輸出中的列的可選名稱。 如果沒有*運算式*,則*列名*是強制性的,並且該名稱的列必須出現在輸入中。 如果省略,將自動生成名稱。 如果*運算式*傳回多個列,則可以在括弧中指定列名稱的清單。 在這種情況下 *,將為表達式*的輸出列指定名稱,刪除輸出列的所有其餘部分(如果有)。 如果未指定列名稱的清單,則所有*運算式*的輸出列都將與生成的名稱一起添加到輸出中。
* ** 參考輸入資料行的選擇性純量運算式。 如果未省略*列名*,則*表達式*為必填項。

    所傳回的新計算資料行名稱可以和輸入中的現有資料行同名。

**傳回**

具有命名為引數的資料行，且資料列數目和輸入資料表相同的資料表。

**範例**

下列範例示範幾種可以使用 `project` 運算子完成的操作。 輸入資料表 `T` 有三個類型為 `int` 的資料行：`A`、`B` 和 `C`。 

```kusto
T
| project
    X=C,                       // Rename column C to X
    A=2*B,                     // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B                      // Calculate a new column B from the old B
```

[series_stats](series-statsfunction.md)是返回多個列的函數的範例。