---
title: 空值 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的空值。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c3a48fdfca855a1ff2f848d4ed97d8162e97b931
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765954"
---
# <a name="null-values"></a>Null 值

Kusto 中的所有標量數據類型都有表示缺失值的特殊值。
此值為 null**值**,或簡稱**null**。

## <a name="null-literals"></a>空白文字

標量類型的`T`null 值由`T(null)`null 文本 在查詢語言中表示。
因此,以下返回一個滿空的行:

```kusto
print bool(null), datetime(null), dynamic(null), guid(null), int(null), long(null), real(null), double(null), time(null)
```

> [!WARNING]
> 請注意,`string`目前類型不支援空值。

## <a name="comparing-null-to-something"></a>將空與某物進行比較

null 值不等於數據類型的任何其他值,包括自身。 (即`null == null`,為 false。要確定某個值是否為 null 值,請使用[isnull()](../isnullfunction.md)函數和[isnotnull()](../isnotnullfunction.md)函數。

## <a name="binary-operations-on-null"></a>對空的二進位作業

通常,null 以「粘性」的方式圍繞二進位運算符執行;空值和任何其他值(包括另一個空值)之間的二進位操作生成空值。

## <a name="data-ingestion-and-null-values"></a>資料引入與空值

對於大多數資料類型,資料源中的缺失值在相應的表單元格中生成 null 值。 例外情況是類型`string`列和類似 CSV 的引入,其中缺少的值生成空字串。
因此,例如,如果我們有: 

```kusto
.create table T [a:string, b:int]

.ingest inline into table T
[,]
[ , ]
[a,1]
```

然後：

|a     |b     |是空(a)|是空的(a)|斯特倫(a)|是空(b)|
|------|------|---------|----------|---------|---------|
|&nbsp;|&nbsp;|false    |true      |0        |true     |
|&nbsp;|&nbsp;|false    |false     |1        |true     |
|a     |1     |false    |false     |1        |false    |

::: zone pivot="azuredataexplorer"

* 如果在 Kusto.Explorer 執行此查詢,`true`則所有值都將解說`1`為`false`,所有值會`0`顯示為 。

::: zone-end

* Kusto 不提供一種約束表列具有 null 值的方法(換句話說,沒有等效於`NOT NULL`SQL 的約束)。
