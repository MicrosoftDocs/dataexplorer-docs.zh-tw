---
title: '範圍 ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中 ( # A1 的範圍。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9a37d375ca83252b063821659f0b5490337c6667
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241054"
---
# <a name="range"></a>range()

產生包含一系列平均間距值的動態陣列。

## <a name="syntax"></a>語法

`range(`*開始* `,`*停止*[ `,` *步驟*]`)` 

## <a name="arguments"></a>引數

* *start*：結果陣列中第一個元素的值。 
* *停止*：結果陣列中最後一個專案的值，或大於所產生陣列中最後一個元素的最小值，以及從*start 開始**的整數*倍數內的最小值。
* *步驟*：陣列的兩個連續元素之間的差異。 *Step*的預設值是 `1` 針對數值和 `1h` `timespan` 或`datetime`

## <a name="examples"></a>範例

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
