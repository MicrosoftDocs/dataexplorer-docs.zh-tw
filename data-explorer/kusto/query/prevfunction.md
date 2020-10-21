---
title: '上一個 ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中的前 ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0ef9fe5160d433554880ac1be0c4a3409d286f17
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249598"
---
# <a name="prev"></a>prev()

傳回指定資料列中特定資料行的值。
指定的資料列位於 [序列化資料列集](./windowsfunctions.md#serialized-row-set)內目前資料列的指定位移。

## <a name="syntax"></a>語法

有幾種可能性。

* `prev(column)`

* `prev(column, offset)`

* `prev(column, offset, default_value)`

## <a name="arguments"></a>引數

* `column`：要從中取得值的資料行。

* `offset`：在資料列中返回的位移。 如果未指定位移，則會使用預設位移1。

* `default_value`：沒有先前的資料列可從中取得值時，所要使用的預設值。 如果未指定任何預設值，則會使用 null。

## <a name="examples"></a>範例

```kusto
Table | serialize | extend prevA = prev(A,1)
| extend diff = A - prevA
| where diff > 1

Table | serialize prevA = prev(A,1,10)
| extend diff = A - prevA
| where diff <= 10
```
