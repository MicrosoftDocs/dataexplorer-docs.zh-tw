---
title: 資料行管理-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的資料行管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 92f432f1367fb28f3343c4f63b6ccf62679ee336
ms.sourcegitcommit: 487485c87706183a9824f695e5b56b0bc5ade048
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89056246"
---
# <a name="columns-management"></a>資料行管理

本節說明用來管理資料表資料行的下列控制命令：

|Command |描述 |
|------- | -------|
|[改變數據行](alter-column.md) |改變現有資料表資料行的資料類型 |
|[alter-merge 資料表資料行](alter-merge-table-column.md) 和 [alter table 資料行-docstrings](alter-merge-table-column.md#alter-table-column-docstrings) | 設定 `docstring` 指定之資料表的一或多個資料行的屬性。
|[`.alter table`](alter-table-command.md), [`.alter-merge table`](alter-table-command.md) | 修改資料表的架構 (新增/移除資料行)  |
|[drop column 和 drop table 資料行](drop-column.md) |從資料表中移除一個或多個資料行 |
|[重新命名資料行或資料行](rename-column.md) |變更現有或多個資料表資料行的名稱 |
