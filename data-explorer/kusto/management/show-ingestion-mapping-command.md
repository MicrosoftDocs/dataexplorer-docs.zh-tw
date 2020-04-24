---
title: .顯示引入映射 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 .show 引入映射。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 91990fe40664ae89d69357812b0def2c7288eb7d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519817"
---
# <a name="show-ingestion-mappings"></a>.顯示引入對應

顯示引入對應(全部或名稱指定的映射)。

* `.show``table`*表名*`ingestion`*對應*  `mappings`

* `.show``table`*表名*`ingestion`*對應金德*`mapping`*映射名稱*   

顯示所有映射類型的所有引入對應:

* `.show``table`*表格名稱*`ingestion`  `mapping`
 
**範例** 
 
```
.show table MyTable ingestion csv mapping "Mapping1" 

.show table MyTable ingestion csv mappings 

.show table MyTable ingestion mappings 
```

**範例輸出**

| 名稱     | 種類 | 對應     |
|----------|------|-------------|
| 映射1 | CSV  | {"名稱":"行號","DataType":"int","CsvDataType":null,"Ordinal":0,"ConstValue":null,{"名稱":"行吉德","數據類型":"字串","CsvDataType":null,"Ordinal":1,"ConstValue":null] |