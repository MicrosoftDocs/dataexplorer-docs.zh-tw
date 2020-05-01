---
title: 。顯示內嵌對應-Azure 資料總管 |Microsoft Docs
description: 本文說明。在 Azure 資料總管中顯示內嵌對應。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 711861a07896b7bdc4cf57bebbf1cdd0e01d064a
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617165"
---
# <a name="show-ingestion-mappings"></a>。顯示內嵌對應

顯示內嵌對應（全部或依名稱指定的對應）。

* `.show``table` *TableName* TableName `ingestion` *MappingKind*  `mappings`

* `.show``table` *TableName* TableName `ingestion` *MappingKind* MappingKind`mapping` *MappingName*   

顯示所有對應種類的所有內嵌對應：

* `.show``table` *TableName*`ingestion`  `mapping`
 
**範例** 
 
```kusto
.show table MyTable ingestion csv mapping "Mapping1" 

.show table MyTable ingestion csv mappings 

.show table MyTable ingestion mappings 
```

**範例輸出**

| 名稱     | 種類 | 對應     |
|----------|------|-------------|
| mapping1 | CSV  | [{"Name"： "rownumber"，"DataType"： "int"，"CsvDataType"： null，"序數"：0，"ConstValue"： null}，{"Name"： "rowguid"，"DataType"： "string"，"CsvDataType"： null，"序數"：1，"ConstValue"： null}] |
