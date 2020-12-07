---
title: bin() - Azure 資料總管 | Microsoft Docs
description: 本文說明 API 資料總管中的 bin()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 6fc2e55b43e7c7c2dc2bb537730f8f627e3e4a66
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513109"
---
# <a name="bin"></a>bin()

將值捨入為指定 bin 大小的整數倍數。 

常用來與 [`summarize by ...`](./summarizeoperator.md) 搭配使用。
如果您有一組零散值，這些值會分組為一組較小的特定值。

Null 值、Null 的間隔大小或負的間隔大小都會產生 Null。 

`floor()` 函式的別名。

## <a name="syntax"></a>語法

`bin(`*value*`,`*roundTo*`)`

## <a name="arguments"></a>引數

* *value*：數字、日期或時間範圍。 
* *roundTo*：「間隔大小」。 用來分割 value  的數字或時間範圍。 

## <a name="returns"></a>傳回

低於 value 的 roundTo 最接近倍數。  
 
```kusto
(toint((value/roundTo))) * roundTo`
```

## <a name="examples"></a>範例

運算式 | 結果
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1970-05-11 13:45:07), 1d)`|  `datetime(1970-05-11)`


下列運算式會計算值區大小為 1 秒之持續時間的長條圖︰

```kusto
T | summarize Hits=count() by bin(Duration, 1s)
```
