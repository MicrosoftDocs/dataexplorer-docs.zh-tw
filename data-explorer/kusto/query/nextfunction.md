---
title: 下一個（）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的下一個（）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a265d536f655df3086ece1b9953eaade4717781c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346621"
---
# <a name="next"></a>next()

傳回資料列中資料行的值，該資料列在序列化資料列[集中](./windowsfunctions.md#serialized-row-set)的目前資料列之後的某些位移上。

## <a name="syntax"></a>語法

`next(column)`

`next(column, offset)`

`next(column, offset, default_value)`

## <a name="arguments"></a>引數

* `column`：要從中取得值的資料行。

* `offset`：要在資料列中往前進行的位移。 如果沒有指定位移，則會使用預設位移1。

* `default_value`：在沒有下一個資料列要接受值時，所要使用的預設值。 若未指定預設值，則會使用 null。


## <a name="examples"></a>範例
```kusto
Table | serialize | extend nextA = next(A,1)
| extend diff = A - nextA
| where diff > 1

Table | serialize nextA = next(A,1,10)
| extend diff = A - nextA
| where diff <= 10
```