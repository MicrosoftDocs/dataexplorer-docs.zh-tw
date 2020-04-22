---
title: .刪除引入映射 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 .drop 引入映射。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: c1434234033acc73de35289c6bc0a90af727babb
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744760"
---
# <a name="drop-ingestion-mapping"></a>.drop 擷取對應

從資料庫中刪除引入映射。
 
`.drop``table`*表名*`ingestion`*對應金德*`mapping`*映射名稱*   

**範例** 

```kusto
.drop table MyTable ingestion CSV mapping "Mapping1" 

.drop table MyTable ingestion JSON mappings "Mapping1" 
```
