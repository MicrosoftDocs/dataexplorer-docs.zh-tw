---
title: 'row_window_session ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 row_window_session。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f872004a8291adc95f594c6301075faa02c8ec6c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242865"
---
# <a name="row_window_session"></a>row_window_session()

`row_window_session()` 計算序列化資料列 [集中資料行](./windowsfunctions.md#serialized-row-set)的會話開始值。

## <a name="syntax"></a>語法

`row_window_session` `(` *`Expr`* `,` *`MaxDistanceFromFirst`* `,` *`MaxDistanceBetweenNeighbors`* [`,` *`Restart`*] `)`

* *`Expr`* 這是一種運算式，其值會在會話中群組在一起。
  Null 值會產生 null 值，而下一個值會啟動新的會話。
  *`Expr`* 必須是類型的純量運算式 `datetime` 。

* *`MaxDistanceFromFirst`* 建立一個啟動新會話的準則：目前值 *`Expr`* 與 *`Expr`* 會話開始時的值之間的最大距離。
  它是類型的純量常數 `timespan` 。

* *`MaxDistanceBetweenNeighbors`* 建立啟動新會話的第二個準則：從某個值到下一個值的最大距離 *`Expr`* 。
  它是類型的純量常數 `timespan` 。

* *Restart* 是類型的選擇性純量運算式 `boolean` 。 如果有指定，評估為的每個值 `true` 都會立即重新開機會話。

## <a name="returns"></a>傳回

函數會傳回每個會話開始時的值。

**備註**

函數具有下列概念計算模型：

1. 依序移至值的輸入序列 *`Expr`* 。

1. 針對每個值，判斷它是否會建立新的會話。

1. 如果它建立新的會話，就會發出的值 *`Expr`* 。 否則，會發出的先前值 *`Expr`* 。

判斷值是否代表新會話的條件是下列條件的邏輯或其中之一：

* 如果沒有先前的會話值，或先前的會話值為 null。

* 如果的值 *`Expr`* 等於或超過先前的會話值加上 *`MaxDistanceFromFirst`* 。

* 如果的值 *`Expr`* 等於或超過加號的先前值 *`Expr`* *`MaxDistanceBetweenNeighbors`* 。

## <a name="examples"></a>範例

下列範例示範如何計算具有兩個數據行之資料表的會話開始值： `ID` 識別序列的資料行，以及 `Timestamp` 提供每一筆記錄發生時間的資料行。 在此範例中，會話不能超過1小時，而且只要記錄的間隔少於5分鐘，就會繼續進行。

```kusto
datatable (ID:string, Timestamp:datetime) [
    // ...
]
| sort by ID asc, Timestamp asc
| extend SessionStarted = row_window_session(Timestamp, 1h, 5m, ID != prev(ID))
```