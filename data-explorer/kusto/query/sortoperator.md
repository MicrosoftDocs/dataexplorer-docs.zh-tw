---
title: 排序運算符 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的排序運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 638783b28cddc51d64a80096d7d4d6e0f669d354
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507475"
---
# <a name="sort-operator"></a>sort 運算子 

按照一個或多個資料行的順序排序輸入資料表的資料列。

```kusto
T | sort by strlen(country) asc, price desc
```

**別名**

`order`

**語法**

*T*`| sort by``asc` | `nulls first`表示 | 式`nulls last`[ ]`,` [ ] [ ]`desc`*expression*

**引數**

* *T*: 要排序的表輸入。
* *運算式*:要排序的標量運算式。 值的類型必須是數值、日期、時間或字串。
* `asc` 按照遞增順序由低至高排序。 預設值是 `desc`，由高遞減至低。
* `nulls first`(訂單的`asc`預設值)將空值放在開頭,(`nulls last``desc`訂單的 默認值)將空值放在末尾。

**範例**

```kusto
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc nulls first
```

Traces 資料表中具有特定 `ActivityId`的所有資料列，按其時間戳記排序。 如果`Timestamp`列包含空值,則這些值將顯示在結果的第一行。

為了從結果中排除空值,在調用進行排序之前添加篩選器:

```kusto
Traces
| where ActivityId == "479671d99b7b" and isnotnull(Timestamp)
| sort by Timestamp asc
```