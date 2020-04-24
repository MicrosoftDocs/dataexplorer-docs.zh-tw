---
title: 實體引用 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的實體引用。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: abbf63de632ff2ff5fb721dddc256c5ee62d4966
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509396"
---
# <a name="entity-references"></a>實體參考

命名 Kusto 架構實體(資料庫、表、列和存儲的函數,但不是群集)使用名稱在查詢中引用它們。 如果實體的容器在當前上下文中是明確的,則實體名稱可以在沒有附加限定的情況下使用。 例如,當對名為`DB`的資料庫執行查詢時,可以參考該資料庫中僅使用`T`其名稱呼叫的`T`表格 。

另一方面,如果實體的容器不在上下文中可用,或者希望引用上下文中與容器不同的容器中的實體,則必須使用實體的**限定名稱**,即實體名稱與容器的串聯(以及可能與其容器的容器等)的串聯。因此`DB`,針對資料庫執行的查詢可以使用 引用同`T1`一群集不同資料庫`DB1`中的表`database("DB1").T1`, 如果要參考另一個群集中的表,則可以這樣做,例如透過`cluster("https://C2.kusto.windows.net/").database("DB2").T2`使用 。

實體引用也可以實體漂亮名稱,只要它在實體容器的上下文中是唯一的。 請參考[實體的名稱](./entity-names.md#entity-pretty-names)。

## <a name="wildcard-matching-for-entity-names"></a>實體名稱的通配符比

在某些上下文中,可以使用通配符`*`( ) 來匹配實體名稱的全部或部分。 例如,以下查詢引用當前資料庫中的所有表,以及資料庫中`DB`名稱以`T`: 開頭的所有表。

```kusto
union *, database("DB1").T*
```

注意:通配符匹配與以美元符號 ()`$`開頭的實體名稱不匹配。
此類名稱是系統保留的。



