---
title: 擴充運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的擴充運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0398efc3f97e9af1f994b16b91a9888fb4fcfa0b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243327"
---
# <a name="extend-operator"></a>extend 運算子

建立計算結果欄，並將其附加至結果集。

```kusto
T | extend duration = endTime - startTime
```

## <a name="syntax"></a>語法

*T* `| extend` [*columnname*  |  `(` *columnname*[ `,` ...] `)` `=` ]*運算式*[ `,` ...]

## <a name="arguments"></a>引數

* *T*：輸入表格式結果集。
* *ColumnName：* 選。 要加入或更新之資料行的名稱。 如果省略，則會產生名稱。 如果 *運算式* 傳回一個以上的資料行，則可以在括弧中指定資料行名稱的清單。 在此情況下，會為 *運算式*的輸出資料行提供指定的名稱，卸載其餘的輸出資料行（如果有的話）。 如果未指定資料行名稱的清單，則會將所有具有所產生名稱的 *運算式*輸出資料行新增至輸出。
* *運算式：* 輸入之資料行的計算。

## <a name="returns"></a>傳回

輸入表格式結果集的複本，如下所示：
1. `extend`在輸入中已存在的資料行名稱會被移除，並附加為其新的計算值。
2. `extend`輸入中不存在的資料行名稱會附加為其新的計算值。

**提示**

* `extend`運算子會將新的資料行加入至沒有索引的輸入結果**not**集。 在大部分的情況下，如果新的資料行設定為與具有索引的現有資料表資料行完全相同，Kusto 可以自動使用現有的索引。 不過，在某些複雜的案例中，不會進行這項傳播。 在這種情況下，如果目標是要重新命名資料行，請改用[ `project-rename` 運算子](projectrenameoperator.md)。

## <a name="example"></a>範例

```kusto
Logs
| extend
    Duration = CreatedOn - CompletedOn
    , Age = now() - CreatedOn
    , IsSevere = Level == "Critical" or Level == "Error"
```

您可以使用 [series_stats](series-statsfunction.md) 函數傳回多個資料行。