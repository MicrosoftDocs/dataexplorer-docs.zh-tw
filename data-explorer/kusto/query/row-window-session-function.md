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
ms.openlocfilehash: ce8da96733dd483b8600c7cfb3618ed986e9d2b0
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351551"
---
# <a name="row_window_session"></a>row_window_session()

`row_window_session()`計算序列化資料列集中的資料[行](./windowsfunctions.md#serialized-row-set)會話啟動值。

## <a name="syntax"></a>語法

`row_window_session` `(` *`Expr`* `,` *`MaxDistanceFromFirst`* `,` *`MaxDistanceBetweenNeighbors`* [`,` *`Restart`*] `)`

* *`Expr`* 這是一種運算式，其值在會話中會群組在一起。
  Null 值會產生 null 值，而下一個值會啟動新的會話。
  *`Expr`* 必須是類型的純量運算式 `datetime` 。

* *`MaxDistanceFromFirst`* 建立啟動新會話的一個準則：目前值 *`Expr`* 與會話開頭的值之間的最大距離 *`Expr`* 。
  它是類型的純量常數 `timespan` 。

* *`MaxDistanceBetweenNeighbors`* 建立啟動新會話的第二個準則：從的某個值到下一個的最大距離 *`Expr`* 。
  它是類型的純量常數 `timespan` 。

* *Restart*是類型的選擇性純量運算式 `boolean` 。 如果指定，則每個評估為的值 `true` 都會立即重新開機會話。

## <a name="returns"></a>傳回

函數會傳回每個會話開頭的值。

**備註**

函數具有下列概念計算模型：

1. 依序移至值的輸入序列 *`Expr`* 。

1. 針對每個值，判斷它是否建立新的會話。

1. 如果它建立了新的會話，就會發出的值 *`Expr`* 。 否則，會發出先前的值 *`Expr`* 。

判斷值是否代表新會話的條件是下列條件的邏輯或其中之一：

* 如果沒有先前的會話值，或前一個會話的值為 null。

* 如果的值 *`Expr`* 等於或超過先前的會話值加上，則為 *`MaxDistanceFromFirst`* 。

* 如果的值 *`Expr`* 等於或超過先前的加號值，則為 *`Expr`* *`MaxDistanceBetweenNeighbors`* 。

## <a name="examples"></a>範例

下列範例會示範如何計算含有兩個數據行之資料表的會話開始值： `ID` 識別序列的資料行，以及 `Timestamp` 提供每一筆記錄發生時間的資料行。 在此範例中，會話不能超過1小時，而且只要記錄少於5分鐘，它就會繼續。

```kusto
datatable (ID:string, Timestamp:datetime) [
    // ...
]
| sort by ID asc, Timestamp asc
| extend SessionStarted = row_window_session(Timestamp, 1h, 5m, ID != prev(ID))
```