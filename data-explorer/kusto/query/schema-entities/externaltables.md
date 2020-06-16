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
ms.openlocfilehash: a76ef031a3fbcaa26f90fd2c2664920805b823b5
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780264"
---
# <a name="external-tables"></a>外部資料表

**外部資料表**是 Kusto 架構實體，它會參考儲存在 Azure 資料總管資料庫外部的資料。

與[資料表](tables.md)類似，外部資料表具有妥善定義的架構（資料行名稱和資料類型配對的排序清單）。 不同于資料表，資料會儲存在叢集外部並加以管理。 最常見的資料是以某些標準格式（例如 CSV、Parquet、Avro）儲存，而不是由 Azure 資料總管內嵌。

**外部資料表**會建立一次。 請參閱下列命令以建立外部資料表：
* [外部資料表一般控制命令](../../management/externaltables.md)
* [建立和改變外部 SQL 資料表](../../management/external-sql-tables.md)
* [建立和改變 Azure 儲存體或 Azure Data Lake 中的資料表](../../management/external-tables-azurestorage-azuredatalake.md)

您可以使用[external_table （）](../../query/externaltablefunction.md)函數來參考**外部資料表**的名稱。 

**注意事項**

* 外部資料表名稱：
   * 區分大小寫。
   * 不能與 Kusto 資料表名稱重迭。
   * 遵循[機構名稱](./entity-names.md)的規則。
* 每個資料庫的外部資料表上限為1000。
* Kusto 支援對外部資料表進行[匯出](../../management/data-export/export-data-to-an-external-table.md)和[連續匯出](../../management/data-export/continuous-data-export.md)，以及[查詢外部資料表](../../../data-lake-query-data.md)。
* [資料清除](../../concepts/data-purge.md)不會套用至外部資料表。 永遠不會從外部資料表中刪除記錄。
