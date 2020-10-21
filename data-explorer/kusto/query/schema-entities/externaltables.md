---
title: 外部資料表-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的外部資料表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: a2c3580ee265e0c67003d67c7e5a68cdd9165191
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342666"
---
# <a name="external-tables"></a>外部資料表

**外部資料表**是 Kusto 架構實體，可參考儲存在 Azure 資料總管資料庫外部的資料。

與 [資料表](tables.md)類似，外部資料表具有妥善定義的架構， (資料行名稱和資料類型配對的排序清單) 。 不同于資料表，資料會儲存在叢集外部並進行管理。 最常見的資料會以一些標準格式儲存，例如 CSV、Parquet、Avro，而不會由 Azure 資料總管內嵌。

系統會建立一次 **外部資料表** 。 請參閱下列命令以建立外部資料表：
* [外部資料表一般控制項命令](../../management/external-table-commands.md)
* [建立和改變外部 SQL 資料表](../../management/external-sql-tables.md)
* [在 Azure 儲存體或 Azure Data Lake 中建立及改變數據表](../../management/external-tables-azurestorage-azuredatalake.md)

**外部資料表**的名稱可以使用[External_table ( # B1](../../query/externaltablefunction.md)函數來參考。 

**備註**

* 外部資料表名稱：
   * 區分大小寫。
   * 無法與 Kusto 資料表名稱重迭。
   * 遵循 [機構名稱](./entity-names.md)的規則。
* 每個資料庫的外部資料表上限為1000。
* Kusto 支援 [匯出](../../management/data-export/export-data-to-an-external-table.md) 和 [連續匯出](../../management/data-export/continuous-data-export.md) 至外部資料表，以及 [查詢外部資料表](../../../data-lake-query-data.md)。
* [資料清除](../../concepts/data-purge.md) 不會套用至外部資料表。 永遠不會從外部資料表刪除記錄。