---
title: 下一個() - Azure 數據資源管理員 |微軟文件
description: 本文在 Azure 數據資源管理器中介紹下一個()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f45e88942fdf9eb23451e1391866f57ca5f0e21a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512116"
---
# <a name="next"></a>next()

返回行中列的值,該列在[序列化列集](./windowsfunctions.md#serialized-row-set)中的當前行之後以某種偏移量返回該列。

**語法**

`next(column)`

`next(column, offset)`

`next(column, offset, default_value)`

**引數**

* `column`:要從中獲取值的列。

* `offset`:以行的方式前進的偏移量。 如果未指定偏移量,則使用預設偏移量 1。

* `default_value`:當沒有下一行要從中獲取該值時,將使用的默認值。 如果未指定預設值,則使用 null。


**範例**
```kusto
Table | serialize | extend nextA = next(A,1)
| extend diff = A - nextA
| where diff > 1

Table | serialize nextA = next(A,1,10)
| extend diff = A - nextA
| where diff <= 10
```