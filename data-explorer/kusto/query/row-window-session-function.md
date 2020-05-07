---
title: row_window_session （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 row_window_session （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 51bc5e26cdc2d002b29ec435a68421ba8768a7a4
ms.sourcegitcommit: 9fe6ee7db15a5cc92150d3eac0ee175f538953d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2020
ms.locfileid: "82907182"
---
# <a name="row_window_session"></a>row_window_session()

`row_window_session()`計算序列化資料列集中的資料[行](./windowsfunctions.md#serialized-row-set)會話啟動值。

**語法**

`row_window_session` `(` *`Expr`* `,` *`MaxDistanceFromFirst`* `,` *`MaxDistanceBetweenNeighbors`* [`,` *`Restart`*] `)`

* *`Expr`* 這是一種運算式，其值在會話中會群組在一起。
  Null 值會產生 null 值，而下一個值會啟動新的會話。
  *`Expr`* 必須是類型`datetime`的純量運算式。

* *`MaxDistanceFromFirst`* 建立啟動新會話的一個準則：目前值*`Expr`* 與會話開頭的值*`Expr`* 之間的最大距離。
  它是類型`timespan`的純量常數。

* *`MaxDistanceBetweenNeighbors`* 建立啟動新會話的第二個準則：從的某個值*`Expr`* 到下一個的最大距離。
  它是類型`timespan`的純量常數。

* *Restart*是類型`boolean`的選擇性純量運算式。 如果指定，則每個評估為`true`的值都會立即重新開機會話。

**傳回**

函數會傳回每個會話開頭的值。

**注意事項**

函數具有下列概念計算模型：

1. 依序移至值*`Expr`* 的輸入序列。

1. 針對每個值，判斷它是否建立新的會話。

1. 如果它建立了新的會話，就會發出*`Expr`* 的值。 否則，會發出先前的*`Expr`* 值。

判斷值是否代表新會話的條件是下列條件的邏輯或其中之一：

* 如果沒有先前的會話值，或前一個會話的值為 null。

* 如果的值*`Expr`* 等於或超過先前的會話值加*`MaxDistanceFromFirst`* 上，則為。

* 如果的值*`Expr`* 等於或超過先前的*`Expr`* 加號*`MaxDistanceBetweenNeighbors`* 值，則為。

**範例**

下列範例會示範如何計算含有兩個數據行之資料表的會話開始值：識別`ID`序列的資料行，以及提供每`Timestamp`一筆記錄發生時間的資料行。 在此範例中，會話不能超過1小時，而且只要記錄少於5分鐘，它就會繼續。

```kusto
datatable (ID:string, Timestamp:datetime) [
    // ...
]
| sort by ID asc, Timestamp asc
| extend SessionStarted = row_window_session(Timestamp, 1h, 5m, ID != prev(ID))
```