---
title: 上一個（）-Azure 資料總管
description: 本文說明 Azure 資料總管中的上一個（）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4216f691345c7dffd3bb1974e5f82e877ffb70f2
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128983"
---
# <a name="prev"></a>prev()

傳回指定資料列中特定資料行的值。
指定的資料列位於[序列化資料列集中](./windowsfunctions.md#serialized-row-set)目前資料列的指定位移。

**語法**

有幾種可能性。

* `prev(column)`

* `prev(column, offset)`

* `prev(column, offset, default_value)`

**引數**

* `column`：要從中取得值的資料行。

* `offset`：要回到資料列中的位移。 若未指定位移，則會使用預設位移1。

* `default_value`：在沒有先前的資料列要接受值時，所要使用的預設值。 若未指定預設值，則會使用 null。

**範例**

```kusto
Table | serialize | extend prevA = prev(A,1)
| extend diff = A - prevA
| where diff > 1

Table | serialize prevA = prev(A,1,10)
| extend diff = A - prevA
| where diff <= 10
```
