---
title: Kusto IngestionTime 原則管理-Azure 資料總管
description: 本文說明 Azure 資料總管中的 IngestionTime 原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f5ffc7ae648a9254564af0705cda84f3c79da99b
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85966936"
---
# <a name="ingestiontime-policy-command"></a>Ingestiontime 原則命令

IngestionTime 原則是在資料表上設定的選擇性原則（預設為啟用）。
它提供將記錄內嵌到資料表的大約時間。

使用函式可在查詢時存取內嵌時間值 `ingestion_time()` 。

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

若要刪除原則（等於停用）：
```kusto
.delete table table_name policy ingestiontime  
```