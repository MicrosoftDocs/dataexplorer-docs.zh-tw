---
title: bin （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 bin （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 434d32a3b6597d71ea22c182a468d64d7971e6cb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348967"
---
# <a name="bin"></a>bin()

將值捨入為指定 bin 大小的整數倍數。 

常用於搭配使用 [`summarize by ...`](./summarizeoperator.md) 。
如果您有一組零散值，這些值會分組為一組較小的特定值。

Null 值、null 的 bin 大小或負的 bin 大小會產生 null。 

函式 `floor()` 的別名。

## <a name="syntax"></a>語法

`bin(`*值* `,`*roundTo*`)`

## <a name="arguments"></a>引數

* *值*：數位、日期或 timespan。 
* *roundTo*：「bin 大小」。 用來分割 value ** 的數字、日期或時間範圍。 

## <a name="returns"></a>傳回

低於 value** 的 roundTo** 最接近倍數。  
 
```kusto
(toint((value/roundTo))) * roundTo`
```

## <a name="examples"></a>範例

運算是 | 結果
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1970-05-11 13:45:07), 1d)`|  `datetime(1970-05-11)`


下列運算式會計算值區大小為 1 秒之持續時間的長條圖︰

```kusto
T | summarize Hits=count() by bin(Duration, 1s)
```