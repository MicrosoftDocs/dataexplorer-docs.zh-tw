---
title: 前() - Azure 資料資源管理員 |微軟文件
description: 本文在 Azure 資料資源管理器中介紹 prev()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 33f6045333826b21ddc0092e2cc7d5e033c12a96
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744588"
---
# <a name="prev"></a>prev()

返回行中列的值,該列在[序列化列集](./windowsfunctions.md#serialized-row-set)中的當前行之前處於某種偏移量。

**語法**

`prev(column)`

`prev(column, offset)`

`prev(column, offset, default_value)`

**引數**

* `column`:要從中獲取值的列。

* `offset`:要返回行的偏移量。 如果未指定偏移量,則使用預設偏移量 1。

* `default_value`:當沒有要從中獲取該值的行時,將使用的默認值。 如果未指定預設值,則使用 null。


**範例**

```kusto
Table | serialize | extend prevA = prev(A,1)
| extend diff = A - prevA
| where diff > 1

Table | serialize prevA = prev(A,1,10)
| extend diff = A - prevA
| where diff <= 10
```