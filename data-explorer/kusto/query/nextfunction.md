---
title: '接下來 ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的下一個 ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ca9361c0a43a2881f7448312e4f8a5129426e55a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248735"
---
# <a name="next"></a>next()

傳回資料列中資料行的值，其位於序列化資料列 [集](./windowsfunctions.md#serialized-row-set)內目前資料列之後的某個位移。

## <a name="syntax"></a>語法

`next(column)`

`next(column, offset)`

`next(column, offset, default_value)`

## <a name="arguments"></a>引數

* `column`：要從中取得值的資料行。

* `offset`：在資料列中前進的位移。 如果未指定位移，則會使用預設位移1。

* `default_value`：沒有下一個資料列可從中取得值時，所要使用的預設值。 如果未指定任何預設值，則會使用 null。


## <a name="examples"></a>範例
```kusto
Table | serialize | extend nextA = next(A,1)
| extend diff = A - nextA
| where diff > 1

Table | serialize nextA = next(A,1,10)
| extend diff = A - nextA
| where diff <= 10
```