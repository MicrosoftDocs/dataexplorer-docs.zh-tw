---
title: 訂單操作員-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的訂單操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3f552143be8c02cece19030fc7b6f79d5a4bdf4a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241391"
---
# <a name="order-operator"></a>order 運算子 

按照一個或多個資料行的順序排序輸入資料表的資料列。

```kusto
T | order by country asc, price desc
```

> [!NOTE]
> Order 運算子是 sort 運算子的別名。 如需詳細資訊，請參閱 [排序運算子](sortoperator.md)

## <a name="syntax"></a>語法

*T* `| order by` *column* [ `asc`  |  `desc` ] [ `nulls first`  |  `nulls last` ] [ `,` ...]

## <a name="arguments"></a>引數

* *T*：要排序的資料表輸入。
* 資料*行*：用來排序的*T*資料行。 值的類型必須是數值、日期、時間或字串。
* `asc` 按照遞增順序由低至高排序。 預設值是 `desc`，由高遞減至低。
* `nulls first` (order) 的預設值 `asc` 會在一開始就放置 null 值，而 `nulls last` order)  (預設值 `desc` 會將 null 值放在結尾。

