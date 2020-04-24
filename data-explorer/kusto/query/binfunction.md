---
title: bin() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 bin()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3fb827c71fa63fde031a91bc9aec7f0ed108fd5c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517420"
---
# <a name="bin"></a>bin()

將值捨入為指定 bin 大小的整數倍數。 

經常與[`summarize by ...`](./summarizeoperator.md)結合使用。
如果您有一組零散值，這些值會分組為一組較小的特定值。

空值、空條柱大小或負條柱大小將導致空。 

別名要`floor()`起作用。

**語法**

`bin(`*值*`,`*捨入到*`)`

**引數**

* *值*:數位、日期或時間跨度。 
* *捨到*:"箱大小"。 用來分割 value ** 的數字、日期或時間範圍。 

**傳回**

低於 value** 的 roundTo** 最接近倍數。  
 
```kusto
(toint((value/roundTo))) * roundTo`
```

**範例**

運算是 | 結果
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1970-05-11 13:45:07), 1d)`|  `datetime(1970-05-11)`


下列運算式會計算值區大小為 1 秒之持續時間的長條圖︰

```kusto
T | summarize Hits=count() by bin(Duration, 1s)
```