---
title: 排序運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的排序運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8823b0a6bb15898a9bb15ed00919fa57d75f8e25
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245813"
---
# <a name="sort-operator"></a>sort 運算子 

按照一個或多個資料行的順序排序輸入資料表的資料列。

```kusto
T | sort by strlen(country) asc, price desc
```

**別名**

`order`

## <a name="syntax"></a>語法

*T* `| sort by` *運算式*[] [] `asc`  |  `desc` `nulls first`  |  `nulls last` [ `,` ...]

## <a name="arguments"></a>引數

* *T*：要排序的資料表輸入。
* *運算式*：用來排序的純量運算式。 值的類型必須是數值、日期、時間或字串。
* `asc` 按照遞增順序由低至高排序。 預設值是 `desc`，由高遞減至低。
* `nulls first` (order) 的預設值 `asc` 會在一開始就放置 null 值，而 `nulls last` order)  (預設值 `desc` 會將 null 值放在結尾。

## <a name="example"></a>範例

```kusto
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc nulls first
```

Traces 資料表中具有特定 `ActivityId`的所有資料列，按其時間戳記排序。 如果 `Timestamp` 資料行包含 null 值，則會出現在結果的第一行。

為了從結果中排除 null 值，請在呼叫排序之前加入篩選：

```kusto
Traces
| where ActivityId == "479671d99b7b" and isnotnull(Timestamp)
| sort by Timestamp asc
```