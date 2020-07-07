---
title: 卸載資料行-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 drop 資料行。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 770f787493828e897600485c282f3ccb12bb74c4
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85966868"
---
# <a name="drop-column"></a>.drop 資料行

從資料表中移除資料行。
若要卸載資料表中的多個資料行，請參閱[下面](#drop-table-columns)的。

> [!WARNING]
> 此命令無法復原。 將會刪除資料行中移除的所有資料。
> 未來用來新增該資料行的命令將無法還原資料。

**語法**

`.drop``column` *TableName* `.` *ColumnName*

## <a name="drop-table-columns"></a>捨棄資料表資料行

從資料表中移除一些資料行。

> [!WARNING]
> 此命令無法復原。 將會刪除資料行中移除的所有資料。
> 未來用來新增這些資料行的命令將無法還原資料。

**語法**

`.drop``table` *TableName* `columns` TableName `(`*Col1* [ `,` *Col2*] .。。`)`