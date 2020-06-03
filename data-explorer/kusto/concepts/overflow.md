---
title: 溢位-Azure 資料總管
description: 本文說明 Azure 資料總管中的溢位。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 063165c91319ef5f4183a8ce8f83364fd8188072
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/03/2020
ms.locfileid: "84328954"
---
# <a name="overflows"></a>溢出

當計算的結果對目的地類型而言太大時，就會發生溢位。
溢位通常會導致[部分查詢失敗](partialqueryfailures.md)。

例如，下列查詢會導致溢位。

```kusto
let Weight = 92233720368547758;
range x from 1 to 3 step 1
| summarize percentilesw(x, Weight * 100, 50)
```

Kusto 的 `percentilesw()` 執行 `Weight` 會針對「夠近」的值，累計運算式。
在此情況下，累積會觸發溢位，因為它不符合帶正負號的64位整數。

通常，溢位是查詢中的「bug」結果，因為 Kusto 會使用64位類型來進行算數運算。
最佳做法是查看錯誤訊息，並識別觸發溢位的函數或匯總。 請確定輸入引數會評估為有意義的值。
