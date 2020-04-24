---
title: 訂單運算符 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的順序運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9e2c2adb86f1eb705856e95f8b8f4ee329cb3b43
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511963"
---
# <a name="order-operator"></a>order 運算子 

按照一個或多個資料行的順序排序輸入資料表的資料列。

```kusto
T | order by country asc, price desc
```

**別名**

[sort 運算子](sortoperator.md)

**語法**

*T*`| sort by``asc` | 欄`nulls first`[`nulls last`]`,` [ ] [ ] [`desc` | *column*

**引數**

* *T*: 要排序的表輸入。
* *列*:要排序的*T*欄。 值的類型必須是數值、日期、時間或字串。
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