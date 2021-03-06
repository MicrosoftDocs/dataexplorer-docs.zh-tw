---
title: Project 運算子 - Azure 資料總管 | Microsoft Docs
description: 本文說明 Azure 資料總管中的 Project 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: f94003573cab076d8fa83537cb7868e21b9b084c
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513262"
---
# <a name="project-operator"></a>project 運算子

選取要納入、重新命名或捨棄的資料行，以及插入新的計算資料行。 

結果中的資料行順序是由引數順序來指定。 只有引數中指定的資料行才會包含在結果中。 系統會捨棄輸入中的任何其他資料行。  (另請參閱 `extend`)。

```kusto
T | project cost=price*quantity, price
```

## <a name="syntax"></a>語法

*T* `| project` *ColumnName* [`=` *Expression*] [`,` ...]
  
或
  
*T* `| project` [*ColumnName* | `(`*ColumnName*[`,`]`)` `=`] *Expression* [`,` ...]

## <a name="arguments"></a>引數

* *T*：輸入資料表。
* *ColumnName：* 要在輸出中出現的選擇性資料行名稱。 如果沒有 *Expression*，則 *ColumnName* 是強制性的，且該名稱的資料行必須出現在輸入中。 若省略，則會自動產生名稱。 如果 *Expression* 傳回多個資料行，則可以在括弧中指定資料行名稱清單。 在此情況下，*Expression* 的輸出資料行將會獲得指定的名稱，並捨棄其餘所有輸出資料行 (如果有的話)。 如果未指定資料行名稱清單，則會將具有所產生名稱的所有 *Expression* 輸出資料行新增至輸出。
*  參考輸入資料行的選擇性純量運算式。 如果未省略 *ColumnName*，則 *Expression* 是強制性的。

    所傳回的新計算資料行名稱可以和輸入中的現有資料行同名。

## <a name="returns"></a>傳回

具有命名為引數的資料行，且資料列數目和輸入資料表相同的資料表。

## <a name="example"></a>範例

下列範例示範幾種可以使用 `project` 運算子完成的操作。 輸入資料表 `T` 有三個類型為 `int` 的資料行：`A`、`B` 和 `C`。 

```kusto
T
| project
    X=C,                       // Rename column C to X
    A=2*B,                     // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B                      // Calculate a new column B from the old B
```

[series_stats](series-statsfunction.md) 是會傳回多個資料行的函式範例。