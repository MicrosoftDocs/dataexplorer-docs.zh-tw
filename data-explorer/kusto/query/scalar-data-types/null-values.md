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
ms.openlocfilehash: ac8852adb5138bffe10a4726470b1c53d74cec1b
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324375"
---
# <a name="null-values"></a>Null 值

Kusto 中的所有純量資料類型都有代表遺漏值的特殊值。
此值稱為 **null 值**，或只是 **null**。

## <a name="null-literals"></a>Null 常值

純量類型的 null 值 `T` 會以查詢語言以 null 常值表示 `T(null)` 。
因此，下列內容會傳回完整為 null 的單一資料列：

```kusto
print bool(null), datetime(null), dynamic(null), guid(null), int(null), long(null), real(null), double(null), time(null)
```

> [!WARNING]
> 請注意，目前 `string` 型別不支援 null 值。

## <a name="comparing-null-to-something"></a>比較 null 與某個事物

Null 值不會比較是否等於資料類型的任何其他值，包括其本身。  (，則為 `null == null` false。 ) 若要判斷某個值是否為 null 值，請使用 [isnull ( # B3 ](../isnullfunction.md) 函數和 [isnotnull ( # B5 ](../isnotnullfunction.md) 函數。

## <a name="binary-operations-on-null"></a>Null 的二進位運算

一般情況下，null 在二元運算子周圍會以「粘滯」方式運作;null 值與任何其他值之間的二進位運算 (包括另一個 null 值) 會產生 null 值。

## <a name="data-ingestion-and-null-values"></a>資料內嵌和 null 值

針對大部分的資料類型，資料來源中的遺漏值會在對應的資料表單元格中產生 null 值。 的例外狀況是類型的資料行 `string` 和類似 CSV 的內嵌，其中遺漏值會產生空字串。
例如，假設我們有： 

```kusto
.create table T [a:string, b:int]

.ingest inline into table T
[,]
[ , ]
[a,1]
```

然後：

|a     |b     |) 的 isnull (|isempty () |strlen () |isnull (b) |
|------|------|---------|----------|---------|---------|
|&nbsp;|&nbsp;|false    |true      |0        |true     |
|&nbsp;|&nbsp;|false    |false     |1        |true     |
|a     |1     |false    |false     |1        |false    |

::: zone pivot="azuredataexplorer"

* 如果您在 Kusto 中執行上述查詢，則所有 `true` 值都會顯示為 `1` ，而且所有 `false` 值都會顯示為 `0` 。

* Kusto 不會提供一種方式來限制資料表的資料行，使其不會有 null 值 (換句話說，就是 SQL 的 `NOT NULL` 條件約束) 不相等。

::: zone-end

::: zone pivot="azuremonitor"

* Kusto 不會提供一種方式來限制資料表的資料行，使其不會有 null 值 (換句話說，就是 SQL 的 `NOT NULL` 條件約束) 不相等。

::: zone-end