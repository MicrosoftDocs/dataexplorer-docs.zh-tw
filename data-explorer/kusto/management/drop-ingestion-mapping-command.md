---
title: 。卸載內嵌對應-Azure 資料總管 |Microsoft Docs
description: 本文說明如何在 Azure 資料總管中放置內嵌對應。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 7454bd86a6ca2a835dc0515a9c8901a444259f12
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294520"
---
# <a name="drop-ingestion-mapping"></a>.drop 擷取對應

從資料庫卸載內嵌對應。
 
`.drop``table` *TableName* `ingestion` *MappingKind* `mapping` *MappingName*   

**範例** 

```kusto
.drop table MyTable ingestion CSV mapping "Mapping1" 

.drop table MyTable ingestion json mapping "Mapping1" 
```
