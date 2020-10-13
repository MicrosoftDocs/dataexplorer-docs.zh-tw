---
title: Kusto IngestionTime 原則管理-Azure 資料總管
description: 熟悉 Azure 資料總管中的 IngestionTime 原則命令。 瞭解如何存取內嵌時間，以及瞭解如何開啟和關閉此原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2e1671373547a4ed069d67bc9153248f23bc3b5e
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "92003113"
---
# <a name="ingestiontime-policy-command"></a>Ingestiontime 原則命令

IngestionTime 原則是在資料表上設定的選擇性原則 (預設會啟用) 。
它會提供將記錄內嵌至資料表的大約時間。

您可以使用函數在查詢時存取內嵌時間值 `ingestion_time()` 。

```kusto
T | extend ingestionTime = ingestion_time()
```

若要啟用/停用原則：
```kusto
.alter table table_name policy ingestiontime true
```

若要啟用/停用多個資料表的原則：
```kusto
.alter tables (table_name [, ...]) policy ingestiontime true
```

若要查看原則：
```kusto
.show table table_name policy ingestiontime  

.show table * policy ingestiontime  
```

若要刪除原則 (等於停用) ：
```kusto
.delete table table_name policy ingestiontime  
```