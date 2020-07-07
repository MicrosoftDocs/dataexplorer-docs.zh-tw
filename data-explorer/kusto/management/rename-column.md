---
title: 重新命名資料行-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 [重新命名] 資料行。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 25d0ff106d406c59c24d26542a8dad1e4992e311
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967378"
---
# <a name="rename-column"></a>。重新命名資料行

變更現有資料表資料行的名稱。
若要變更多個資料行的名稱，請參閱[下面](#rename-columns)的。

**語法**

`.rename``column`[*DatabaseName* `.` ] *TableName* `.` *ColumnExistingName* `to` *ColumnNewName*

其中*DatabaseName*、 *TableName*、 *ColumnExistingName*和*ColumnNewName*是個別實體的名稱，並遵循[識別碼命名規則](../query/schema-entities/entity-names.md)。

## <a name="rename-columns"></a>重新命名資料行

變更相同資料表中多個現有資料行的名稱。

**語法**

`.rename``columns` *Col1* `=` [*DatabaseName* `.` [*TableName* `.` *Col2*]] `,` .。。

命令可以用來交換兩個數據行的名稱（每個都重新命名為另一個名稱）。