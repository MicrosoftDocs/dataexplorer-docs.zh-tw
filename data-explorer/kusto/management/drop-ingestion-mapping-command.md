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
ms.openlocfilehash: 69ab4849d67d7cc7507bab075b0111fbd8ec95e7
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780604"
---
# <a name="drop-ingestion-mapping"></a>.drop 擷取對應

從資料庫卸載內嵌對應。
 
`.drop``table` *TableName* `ingestion` *MappingKind* `mapping` *MappingName*   

**範例** 

```kusto
.drop table MyTable ingestion csv mapping "Mapping1" 

.drop table MyTable ingestion json mapping "Mapping1" 
```
