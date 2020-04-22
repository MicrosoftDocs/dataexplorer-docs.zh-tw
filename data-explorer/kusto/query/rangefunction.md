---
title: 範圍() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的範圍()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 86558591e6312edd218230cda19a4afc17a13b27
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744574"
---
# <a name="range"></a>range()

生成包含一系列等間距值的動態數位。

**語法**

`range(`*開始*`,`*停止*`,`(*步驟*]`)` 

**引數**

* *開始*:結果陣列中第一個元素的值。 
* *停止*:結果陣列中最後一個元素的值,或大於結果陣列中最後一個元素的最小值,並且從*開始*開始在*整*數倍內。
* *步驟*:陣列的兩個連續元素之間的差異。 *步長*預設值為`1`數字和`1h``timespan``datetime`

**範例**

下列範例會傳回 `[1, 4, 7]`：

```kusto
T | extend r = range(1, 8, 3)
```

下列範例會傳回保有 2015 年所有天數的陣列︰

```kusto
T | extend r = range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

下列範例會傳回 `[1,2,3]`：

```kusto
range(1, 3)
```

下列範例會傳回 `["01:00:00","02:00:00","03:00:00","04:00:00","05:00:00"]`：

```kusto
range(1h, 5h)
```
