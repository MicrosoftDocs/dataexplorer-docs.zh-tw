---
title: RowOrder 原則-Azure 資料總管
description: 本文說明 Azure 資料總管中的 RowOrder 原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: bf8844a72d2b4fc3d9dec4f24fdfa6ac36ee84d7
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967497"
---
# <a name="roworder-policy-command"></a>roworder 原則命令

本文描述用來建立和變更資料[列順序原則](../management/roworderpolicy.md)的控制命令。

## <a name="show-roworder-policy"></a>顯示 RowOrder 原則

```kusto
.show table <table_name> policy roworder

.show table * policy roworder
```

## <a name="delete-roworder-policy"></a>刪除 RowOrder 原則

```kusto
.delete table <table_name> policy roworder
```

## <a name="alter-roworder-policy"></a>改變 RowOrder 原則

```kusto
.alter table <table_name> policy roworder (<row_order_policy>)

.alter tables (<table_name> [, ...]) policy roworder (<row_order_policy>)

.alter-merge table <table_name> policy roworder (<row_order_policy>)
```

**範例** 

下列範例會將資料行（遞增）上的資料列順序原則設定 `TenantId` 為主鍵，並將資料行 `Timestamp` （遞增）設為次要索引鍵。 然後會查詢原則。

```kusto
.alter table events policy roworder (TenantId asc, Timestamp desc)

.alter tables (events1, events2, events3) policy roworder (TenantId asc, Timestamp desc)

.show table events policy roworder 
```

|TableName|RowOrderPolicy| 
|---|---|
|活動|（TenantId asc，時間戳記 desc）|
