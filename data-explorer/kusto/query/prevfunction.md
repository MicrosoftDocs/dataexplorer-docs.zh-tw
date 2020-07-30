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
ms.openlocfilehash: fb781834d77aee678103a14714721ff0d46f7b3a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346094"
---
# <a name="prev"></a>prev()

傳回指定資料列中特定資料行的值。
指定的資料列位於[序列化資料列集中](./windowsfunctions.md#serialized-row-set)目前資料列的指定位移。

## <a name="syntax"></a>語法

有幾種可能性。

* `prev(column)`

* `prev(column, offset)`

* `prev(column, offset, default_value)`

## <a name="arguments"></a>引數

* `column`：要從中取得值的資料行。

* `offset`：要回到資料列中的位移。 若未指定位移，則會使用預設位移1。

* `default_value`：在沒有先前的資料列要接受值時，所要使用的預設值。 若未指定預設值，則會使用 null。

## <a name="examples"></a>範例

```kusto
Table | serialize | extend prevA = prev(A,1)
| extend diff = A - prevA
| where diff > 1

Table | serialize prevA = prev(A,1,10)
| extend diff = A - prevA
| where diff <= 10
```
