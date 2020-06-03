---
title: 。顯示內嵌對應-Azure 資料總管
description: 本文說明。在 Azure 資料總管中顯示內嵌對應。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 3c19426410046d7ff2357b4967333db8b039d9e6
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/03/2020
ms.locfileid: "84328988"
---
# <a name="show-ingestion-mappings"></a>。顯示內嵌對應

顯示內嵌對應（全部或依名稱指定的對應）。

* `.show``table` *TableName* `ingestion` *MappingKind*  `mappings`

* `.show``table` *TableName* `ingestion` *MappingKind* `mapping` *MappingName*   

顯示所有對應類型的所有內嵌對應：

* `.show` `table` *TableName* `ingestion`  `mapping`
 
**範例** 
 
```kusto
.show table MyTable ingestion csv mapping "Mapping1" 

.show table MyTable ingestion csv mappings 

.show table MyTable ingestion mappings 
```

**範例輸出**

| 名稱     | 種類 | 對應     |
|----------|------|-------------|
| mapping1 | CSV  | `[{"Name":"rownumber","DataType":"int","CsvDataType":null,"Ordinal":0,"ConstValue":null},{"Name":"rowguid","DataType":"string","CsvDataType":null,"Ordinal":1,"ConstValue":null}]` |
