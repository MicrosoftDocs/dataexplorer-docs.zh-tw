---
title: 引入時間策略 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的引入時間策略。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c42da570b961595be1fbcae352fe121d8b6f59ea
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520888"
---
# <a name="ingestiontime-policy"></a>IngestionTime 原則

引入時間策略是表上的可選策略集(預設情況下啟用)。
它提供了將記錄引入表中的大致時間。

可以使用函數在`ingestion_time()`查詢時訪問引入時間值。

```kusto
T | extend ingestionTime = ingestion_time()
```

要啟用/停用策略,請進行以下工作:
```kusto
.alter table table_name policy ingestiontime true
```

要啟用/停用多個表的策略,請進行以下操作:
```kusto
.alter tables (table_name [, ...]) policy ingestiontime true
```

要檢視原則:
```kusto
.show table table_name policy ingestiontime  

.show table * policy ingestiontime  
```

要刪除政策 (等於關閉):
```kusto
.delete table table_name policy ingestiontime  
```