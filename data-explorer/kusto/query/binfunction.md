---
title: 'bin ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 bin ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9bafeee9cec5ac81034b879f054e445d8b118dcf
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243420"
---
# <a name="bin"></a>bin()

將值捨入為指定 bin 大小的整數倍數。 

經常用於與搭配使用 [`summarize by ...`](./summarizeoperator.md) 。
如果您有一組零散值，這些值會分組為一組較小的特定值。

Null 值、null bin 大小或負的 bin 大小將會產生 null。 

`floor()`函數的別名。

## <a name="syntax"></a>語法

`bin(`*值* `,`*roundTo*`)`

## <a name="arguments"></a>引數

* *值*： number、date 或 timespan。 
* *roundTo*：「bin 大小」。 相除 *值*的數位或 timespan。 

## <a name="returns"></a>傳回

低於 value** 的 roundTo** 最接近倍數。  
 
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
