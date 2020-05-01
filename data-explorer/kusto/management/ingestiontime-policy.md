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
ms.openlocfilehash: 907c6ddf84d772f800fce45d3c1245bbd11b0c85
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616451"
---
# <a name="ingestiontime-policy"></a>IngestionTime 原則

IngestionTime 原則是在資料表上設定的選擇性原則（預設為啟用）。
它提供將記錄內嵌到資料表的大約時間。

使用`ingestion_time()`函式可在查詢時存取內嵌時間值。

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