---
title: 溢出 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的溢出。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 22db905788e1313cad2eb229239a390c28bcd5c2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523149"
---
# <a name="overflows"></a>溢出

當計算結果對於目標類型太大時,將發生溢出。
此檔案通常會導致[部份查詢失敗](partialqueryfailures.md)。

例如,以下查詢將導致溢出:

```kusto
let Weight = 92233720368547758;
range x from 1 to 3 step 1
| summarize percentilesw(x, Weight * 100, 50)
```

Kusto`percentilesw()`的 實現累積`Weight`了「 足夠接近」的值的運算式。
在這種情況下,累積會觸發溢出,因為它不適合符號 64 位整數。

但是,通常溢出是查詢中的"bug"的結果,因為 Kusto 使用 64 位元類型進行算術計算。
在這種情況下,最佳操作方案是從函數或聚合觸發溢出的錯誤消息中識別,並確保其輸入參數評估為有意義的值。
