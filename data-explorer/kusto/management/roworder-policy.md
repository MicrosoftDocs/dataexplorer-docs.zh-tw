---
title: 行序策略 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 RowOrder 策略。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: cda4c9a6017071878832fab376a0376d250f3ed6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520242"
---
# <a name="roworder-policy"></a>行Order 政策

本文介紹了用於創建和更改[行訂單策略](../management/roworderpolicy.md)的控制命令。

## <a name="show-roworder-policy"></a>顯示列序原則

```kusto
.show table <table_name> policy roworder

.show table * policy roworder
```

## <a name="delete-roworder-policy"></a>刪除列序原則

```kusto
.delete table <table_name> policy roworder
```

## <a name="alter-roworder-policy"></a>變更列序原則

```kusto
.alter table <table_name> policy roworder (<row_order_policy>)

.alter tables (<table_name> [, ...]) policy roworder (<row_order_policy>)

.alter-merge table <table_name> policy roworder (<row_order_policy>)
```

**範例**

下面的示例將行順序策略設置為`TenantId`列(升序)為主鍵,在`Timestamp`列(升序)上作為輔助鍵;然後查詢政策:

```kusto
.alter table events policy roworder (TenantId asc, Timestamp desc)

.alter tables (events1, events2, events3) policy roworder (TenantId asc, Timestamp desc)

.show table events policy roworder 
```

|TableName|列訂單原則| 
|---|---|
|活動|(租戶 Id asc,時間戳| 