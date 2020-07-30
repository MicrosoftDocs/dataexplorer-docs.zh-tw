---
title: 範圍（）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的範圍（）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2606746e89d645601fa53ed7f81d67ddae203c03
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345907"
---
# <a name="range"></a>range()

產生一個動態陣列，其中包含一系列同等間距的值。

## <a name="syntax"></a>語法

`range(`*啟動* `,`*停止*[ `,` *步驟*]`)` 

## <a name="arguments"></a>引數

* *start*：所產生陣列中第一個元素的值。 
* *stop*：所產生陣列中最後一個元素的值，或是大於所產生陣列中最後一個元素的最小值，以及從*start 開始**的整數*倍數。
* *步驟*：陣列的兩個連續專案之間的差異。 *Step*的預設值是 `1` 適用于數值，而 `1h` 適用于 `timespan` 或`datetime`

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
