---
title: bag_unpack 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的 bag_unpack 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: fd8b0968d90f1c5239cae80c3be9c2a32d0603d6
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225491"
---
# <a name="bag_unpack-plugin"></a>bag_unpack 外掛程式

外掛程式會將 `bag_unpack` `dynamic` 每個屬性包的最上層位置視為資料行，以解壓縮類型的單一資料行。

    T | evaluate bag_unpack(col1)

**語法**

*T*資料 `|` `evaluate` `bag_unpack(` *行* `,` [ *OutputColumnPrefix* ]`)`

**引數**

* *T*：要解壓縮其*資料行的*表格式輸入。
* *Column*：要解除封裝之*T*的資料行。 其型別必須是 `dynamic`。
* *OutputColumnPrefix*：要加入外掛程式所產生之所有資料行的一般前置詞。
  選擇性。

**傳回**

外掛程式會傳回 `bag_unpack` 一個資料表，其中包含多筆記錄做為其表格式輸入（*T*）。 資料表的架構與其表格式輸入相同，並具有下列修改：

* 已移除指定的輸入資料行（資料*行*）。

* 架構的延伸包含的資料行數目，與最上層屬性包值*T*中的相異位置不同。每個資料行的名稱都會對應至每個位置的名稱，並選擇性地加上*OutputColumnPrefix*。 其類型是位置的類型（如果相同位置的所有值都具有相同的類型），或 `dynamic` （如果值的類型不同）。

**注意事項**

外掛程式的輸出架構取決於資料值，使其成為資料本身「無法預測」。 因此，多個具有不同資料輸入的外掛程式執行，可能會產生不同的輸出架構。

外掛程式的輸入資料必須是這樣，輸出架構才符合表格式架構的所有規則。 尤其是：

1. 輸出資料行名稱不能與表格式輸入*T*中的現有資料行相同，除非它是要解除封裝的資料行（資料*行*）本身，因為這會產生兩個具有相同名稱的資料行。

2. 所有位置名稱（在前面加上*OutputColumnPrefix*）必須是有效的機構名稱，並符合[識別碼命名規則](./schema-entities/entity-names.md#identifier-naming-rules)。

**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20}),
    dynamic({"Name": "Dave", "Age":40}),
    dynamic({"Name": "Smitha", "Age":30}),
]
| evaluate bag_unpack(d)
```

|名稱  |Age|
|------|---|
|John  |20 |
|Dave  |40 |
|Smitha|30 |
