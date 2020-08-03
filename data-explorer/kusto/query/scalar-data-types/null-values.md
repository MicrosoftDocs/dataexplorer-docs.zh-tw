---
title: Null 值-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 Null 值。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c493431fcfa22ad0419659a5b6e036205f3bf299
ms.sourcegitcommit: 194453a8eb11c3ccb54c473e887c84cb8e91b939
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87473967"
---
# <a name="null-values"></a>Null 值

Kusto 中的所有純量資料類型都有一個代表遺漏值的特殊值。
這個值稱為**null 值**，或只是**null**。

## <a name="null-literals"></a>Null 常值

純量類型的 null 值 `T` 是以查詢語言以 null 常值表示 `T(null)` 。
因此，下列各項會傳回完整為 null 的單一資料列：

```kusto
print bool(null), datetime(null), dynamic(null), guid(null), int(null), long(null), real(null), double(null), time(null)
```

> [!WARNING]
> 請注意，目前 `string` 類型不支援 null 值。

## <a name="comparing-null-to-something"></a>比較 null 與某個專案

Null 值不會與資料類型的任何其他值（包括本身）相等。 （也就是 `null == null` false）。若要判斷某個值是否為 null 值，請使用[isnull （）](../isnullfunction.md)函式和[isnotnull （）](../isnotnullfunction.md)函數。

## <a name="binary-operations-on-null"></a>Null 上的二進位運算

一般來說，null 在二元運算子前後會以「粘滯」方式運作;null 值與任何其他值（包括另一個 null 值）之間的二元運算會產生 null 值。

## <a name="data-ingestion-and-null-values"></a>資料內嵌和 null 值

對於大部分的資料類型而言，資料來源中的遺漏值會在對應的資料表單元格中產生 null 值。 的例外狀況是類型的資料行 `string` 和類似 CSV 的內嵌，其中遺漏值會產生空字串。
例如，如果我們有： 

```kusto
.create table T [a:string, b:int]

.ingest inline into table T
[,]
[ , ]
[a,1]
```

然後：

|a     |b     |isnull （a）|isempty （a）|strlen （a）|isnull （b）|
|------|------|---------|----------|---------|---------|
|&nbsp;|&nbsp;|false    |true      |0        |true     |
|&nbsp;|&nbsp;|false    |false     |1        |true     |
|a     |1     |false    |false     |1        |false    |

::: zone pivot="azuredataexplorer"

* 如果您在 Kusto 中執行上述查詢，所有的 `true` 值都會顯示為 `1` ，而且所有的 `false` 值都會顯示為 `0` 。

::: zone-end

* Kusto 不會提供方法來限制資料表的資料行是否具有 null 值（換句話說，沒有等同于 SQL 的 `NOT NULL` 條件約束）。
