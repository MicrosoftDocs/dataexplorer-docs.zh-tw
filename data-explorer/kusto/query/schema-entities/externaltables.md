---
title: 外部表 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的外部表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: f4a059ba4533ed91353e75b499e564e6dd06d591
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509379"
---
# <a name="external-tables"></a>外部資料表

**外部表**是引用存儲在 Kusto 資料庫之外的數據的 Kusto 架構實體。

與[表](tables.md)類似,外部表具有定義良好的架構(列名和數據類型對的有序清單)。 與表不同,數據存儲和管理在 Kusto 群集之外。 最常見的是,資料存儲在某些標準格式,如 CSV、Parquet、Avro,並且 Kusto 不會引入。

**外部表**創建一次(請參閱[外部表控件命令](../../management/externaltables.md)),並且可以使用[external_table()](../../query/externaltablefunction.md)函數的名稱引用它的名稱。 

**注意事項**

* 外部表名稱區分大小寫。
* 外部表名稱不能與 Kusto 表名稱重疊。
* 外部表名稱遵循[實體名稱](./entity-names.md)的規則。
* 每個資料庫的外部表的最大限制為 1,000。
* Kusto 支援[資料匯出到外部表](../../management/data-export/export-data-to-an-external-table.md), 以及[查詢外部表](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)。
