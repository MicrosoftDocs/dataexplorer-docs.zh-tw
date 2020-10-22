---
title: 專案-重新排序運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的專案重新排序運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: de8ff4b9256c8f964350bafd64eac15b028f53d4
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356481"
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
* *ColumnNameOrPattern：* 加入至輸出之資料行或資料行萬用字元模式的名稱。
* 針對萬用字元模式： `asc` `desc` 使用名稱的遞增或遞減順序來指定或排序資料行。 如果 `asc` `desc` 指定或未指定，則順序是由相符的資料行所決定，因為它們出現在來源資料表中。

> [!NOTE]
> * 在不明確的 *ColumnNameOrPattern* 比對中，該資料行會出現在符合模式的第一個位置。
> * 指定的資料行 `project-reorder` 是選擇性的。 未明確指定的資料行會顯示為輸出資料表的最後一個資料行。
> * 若要移除資料行，請使用 [`project-away`](projectawayoperator.md) 。
> * 若要選擇要保留的資料行，請使用 [`project-keep`](project-keep-operator.md) 。
> * 若要重新命名資料行，請使用 [`project-rename`](projectrenameoperator.md) 。

## <a name="returns"></a>傳回

資料表，其中包含以運算子引數所指定之順序的資料行。 `project-reorder` 不會重新命名或移除資料表中的資料行，因此，存在於來源資料表中的所有資料行都會出現在結果資料表中。

## <a name="examples"></a>範例

使用三個數據行重新排序資料表， (a，b，c) ，因此第二個數據行 (b) 將會先出現。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print a='a', b='b', c='c'
|  project-reorder b
```

|b|a|c|
|---|---|---|
|b|a|c|

重新排列資料表的資料行，讓開頭為的資料行 `a` 會出現在其他資料行之前。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print b = 'b', a2='a2', a3='a3', a1='a1'
|  project-reorder a* asc
```

|旁|答|3|b|
|---|---|---|---|
|旁|答|3|b|
