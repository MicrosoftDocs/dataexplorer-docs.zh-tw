---
title: 專案-重新排序運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的專案重新排序運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 233f5f7f6e6064b10d1385eaef8a28302368e74b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345992"
---
# <a name="project-reorder-operator"></a>project-reorder operator

重新排序結果輸出中的資料行。

```kusto
T | project-reorder Col2, Col1, Col* asc
```

## <a name="syntax"></a>語法

*T* `| project-reorder` *ColumnNameOrPattern* [ `asc` | `desc` ] [ `,` ...]

## <a name="arguments"></a>引數

* *T*：輸入資料表。
* *ColumnNameOrPattern：* 加入至輸出的資料行或資料行萬用字元模式的名稱。
* 針對萬用字元模式：以 `asc` `desc` 遞增或遞減順序使用其名稱來指定或排序資料行。 如果 `asc` `desc` 未指定或，則順序是由符合來源資料表中的資料行所決定。

## <a name="returns"></a>傳回

包含依運算子引數所指定順序之資料行的資料表。 `project-reorder`不會重新命名或移除資料表中的資料行，因此，存在於來源資料表中的所有資料行都會出現在結果資料表中。

**備註**

- 在不明確的*ColumnNameOrPattern*比對中，資料行會出現在符合模式的第一個位置。
- 指定的資料行 `project-reorder` 是選擇性的。 未明確指定的資料行會顯示為輸出資料表的最後一個資料行。

* 用 [`project-away`](projectawayoperator.md) 來移除資料行。
* 用 [`project-rename`](projectrenameoperator.md) 來重新命名資料行。


## <a name="examples"></a>範例

以三個數據行（a、b、c）重新排序資料表，第二個數據行（b）會先出現。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print a='a', b='b', c='c'
|  project-reorder b
```

|b|a|c|
|---|---|---|
|b|a|c|

重新排列資料表的資料行，讓以開頭的資料行 `a` 出現在其他資料行之前。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print b = 'b', a2='a2', a3='a3', a1='a1'
|  project-reorder a* asc
```

|賦值|a2|a3|b|
|---|---|---|---|
|賦值|a2|a3|b|
