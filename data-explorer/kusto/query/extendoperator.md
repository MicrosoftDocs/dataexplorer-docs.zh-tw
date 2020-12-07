---
title: extend 運算子 - Azure 資料總管 | Microsoft Docs
description: 本文說明 Azure 資料總管中的 extend 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 7ead6313128b99357dd61f18f55c5d5543943a05
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513347"
---
# <a name="extend-operator"></a>extend 運算子

建立計算結果欄，並將其附加至結果集。

```kusto
T | extend duration = endTime - startTime
```

## <a name="syntax"></a>語法

*T* `| extend` [*ColumnName* | `(`*ColumnName*[`,` ...]`)` `=`] *Expression* [`,` ...]

## <a name="arguments"></a>引數

* *T*：輸入表格式結果集。
* *ColumnName：* 選擇性。 要新增或更新的資料行名稱。 如果省略，則會產生名稱。 如果 *Expression* 傳回多個資料行，則可以在括弧中指定資料行名稱清單。 在此情況下，*Expression* 的輸出資料行將會獲得指定的名稱，並捨棄其餘輸出資料行 (如果有的話)。 如果未指定資料行名稱清單，則會將具有所產生名稱的所有 *Expression* 輸出資料行新增至輸出。
* *運算式：* 對輸入的資料行進行計算。

## <a name="returns"></a>傳回

輸入表格式結果集的複本，以致：
1. `extend` 所記下、已存在於輸入中的資料行名稱會遭到移除，並以其新計算值的形式予以附加。
2. `extend` 所記下、不存在於輸入中的資料行名稱會以其新計算值的形式予以附加。

**提示**

* `extend` 運算子會將新的資料行新增至輸入結果集，這 **不會** 有索引。 在大多數情況下，如果新的資料行設定為與具有索引的現有資料表資料行完全相同，則 Kusto 可以自動使用現有的索引。 不過，在某些複雜的案例中，則不會進行這項傳播。 在這類情況下，如果目標是要重新命名資料行，則請改用 [`project-rename` 運算子](projectrenameoperator.md)。

## <a name="example"></a>範例

```kusto
Logs
| extend
    Duration = CreatedOn - CompletedOn
    , Age = now() - CreatedOn
    , IsSevere = Level == "Critical" or Level == "Error"
```

您可以使用 [series_stats](series-statsfunction.md) 函式來傳回多個資料行。