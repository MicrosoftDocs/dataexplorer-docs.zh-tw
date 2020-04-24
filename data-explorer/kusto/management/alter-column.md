---
title: .alter 列 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 .alter 列。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: d41b4f452125fbfebc319112db244deaca79f37a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522605"
---
# <a name="alter-column"></a>.alter 列

更改現有表列的數據類型。

> [!WARNING]
> 變更欄位的資料型態時,該欄中任何未採用新資料類型的預先存在的資料都將在以後的查詢中忽略,並將取代為[空值](../query/scalar-data-types/null-values.md)。 使用`alter column`后無法恢復該數據,即使使用另一個命令將列類型更改回以前的值也是如此。
> 有關在不遺失資料的情況下更改欄型態的建議過程,請參閱[下文](#changing-column-type-without-data-loss)。

**語法** 

`.alter``column` 【*資料庫名稱*`.`】*表名*`.`*欄位欄位*`type``=`*ColumnNewType*
 
**範例** 

```
.alter column ['Table'].['ColumnX'] type=string
```

## <a name="changing-column-type-without-data-loss"></a>變更欄型態而不遺失資料

要更改列類型,同時保留歷史資料,請創建一個新的正確鍵入的表。

對於要變更`T1`欄型態的每個表,請執行以下步驟:

1. 創建具有正確`T1_prime`架構(正確的列類型)的表。
1. 使用[.重新命名表](rename-table-command.md)指令交換表,該命令允許交換表名稱。

```
.rename tables T_prime=T1, T1=T_prime
```

命令完成後,新數據將流向`T1`現在正確鍵入的數據,並且歷史數據在`T1_prime`中可用。

在`T1_prime`數據超出保留期之前,需要更改觸摸`T1`的查詢以`T1_prime`與執行聯合。