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
ms.openlocfilehash: 7f74732ed38d0b41a857fc549f549ce54ad4dce6
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863705"
---
# <a name="external-tables"></a>外部資料表

**外部資料表**是 Kusto 架構實體，它會參考儲存在 Kusto 資料庫外部的資料。

與[資料表](tables.md)類似，外部資料表具有妥善定義的架構（資料行名稱和資料類型配對的排序清單）。 不同于資料表，資料會儲存在 Kusto 叢集外部並加以管理。 最常見的資料是以某種標準格式（例如 CSV、Parquet、Avro）儲存，而不是由 Kusto 內嵌。

**外部資料表**只會建立一次（請參閱[外部資料表一般控制命令](../../management/externaltables.md)、[建立和變更外部 SQL 資料表](../../management/external-sql-tables.md)，以及[建立和變更儲存體中的資料表](../../management/external-tables-azurestorage-azuredatalake.md)），並可使用[external_table （）](../../query/externaltablefunction.md)函數來參考其名稱。 

**注意事項**

* 外部資料表名稱會區分大小寫。
* 外部資料表名稱不能與 Kusto 資料表名稱重迭。
* 外部資料表名稱會遵循[機構名稱](./entity-names.md)的規則。
* 每個資料庫的外部資料表上限為1000。
* Kusto 支援[將資料匯出至外部資料表](../../management/data-export/export-data-to-an-external-table.md)以及[查詢外部資料表](../../../data-lake-query-data.md)。
