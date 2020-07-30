---
title: order 運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的訂單操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 510e2de8a30a422955c0cbfcbdf3a0f50e46dbc5
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346553"
---
# <a name="order-operator"></a>order 運算子 

按照一個或多個資料行的順序排序輸入資料表的資料列。

```kusto
T | order by country asc, price desc
```

> [!NOTE]
> Order 運算子是 sort 運算子的別名。 如需詳細資訊，請參閱[sort 運算子](sortoperator.md)

## <a name="syntax"></a>語法

*T*資料 `| order by` *行*[ `asc`  |  `desc` ] [ `nulls first`  |  `nulls last` ] [ `,` ...]

## <a name="arguments"></a>引數

* *T*：要排序的資料表輸入。
* *column*：要排序之*T*的資料行。 值的類型必須是數值、日期、時間或字串。
* `asc` 按照遞增順序由低至高排序。 預設值是 `desc`，由高遞減至低。
* `nulls first`（order 的預設值 `asc` ）會將 null 值放在開頭， `nulls last`（order 的預設值 `desc` ）會將 null 值放在結尾。

