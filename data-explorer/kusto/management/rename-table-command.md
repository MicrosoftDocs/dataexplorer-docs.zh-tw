---
title: 。重新命名資料表和重新命名資料表-Azure 資料總管 |Microsoft Docs
description: 本文說明。在 Azure 資料總管中重新命名資料表和重新命名資料表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: a1644021d1e2df294782d3b24e111d3c57af5f91
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617573"
---
# <a name="rename-table-and-rename-tables"></a>。重新命名資料表和. 重新命名資料表

變更現有資料表的名稱。

此`.rename tables`命令會將資料庫中的資料表數目名稱變更為單一交易。

需要[資料庫管理員許可權](../management/access-control/role-based-authorization.md)。

**語法**

`.rename``table` *OldName* OldName `to` *NewName*

`.rename``tables` * *NewName = *OldName* [`ifexists`] [`,` ...]

> [!NOTE]
> * *OldName*是現有資料表的名稱。 如果*OldName*不`ifexists`是現有資料表的名稱（在此情況下，會忽略 rename 命令的這個部分），則會引發錯誤，而且整個命令會失敗（不會有任何作用）。
> * *NewName*是現有資料表的新名稱，用來呼叫*OldName*。
> * 如果`ifexists`指定了，它會修改命令的行為，以忽略不存在之資料表的重新命名部分。

**備註**

此命令只會在範圍中的資料庫資料表上操作。
資料表名稱不能以叢集或資料庫名稱限定。

此命令不會建立新的資料表，也不會移除現有的資料表。
命令所描述的轉換必須讓資料庫中的資料表數目不會變更。

**命令可**支援交換資料表名稱或更複雜的排列，只要它們符合上述規則即可。 例如，將資料內嵌到多個臨時表，然後在單一交易中與現有的資料表交換。

**範例**

假設有下列資料表的資料庫`A`：、 `B`、 `C`和。 `A_TEMP`
下列命令會交換`A`和`A_TEMP` （如此一來， `A_TEMP`現在將會呼叫`A`資料表，而另一種方法是將其`B`重新`NEWB`命名為， `C`並保持不維持）。 

```kusto
.rename tables A=A_TEMP, NEWB=B, A_TEMP=A
``` 

下列命令順序：
1. 建立新的臨時表
1. 以新的資料表取代現有或不存在的資料表

```kusto
// Drop the temporary table if it exists
.drop table TempTable ifexists

// Create a new table
.set TempTable <| ...

// Swap the two tables
.rename tables TempTable=Table ifexists, Table=TempTable

// Drop the temporary table (which used to be Table) if it exists
.drop table TempTable ifexists
```
