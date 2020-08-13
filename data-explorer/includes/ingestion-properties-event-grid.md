---
title: 包含檔案
description: 包含檔案
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 08/12/2020
ms.author: orspodek
ms.reviewer: tzgitlin
ms.custom: include file
ms.openlocfilehash: f63288ad25363f1d32184e45efb21b69a894da0c
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88201728"
---
|**屬性** | **屬性描述**|
|---|---|
| `rawSizeBytes` | 未經壓縮) 資料的原始 (大小。 針對 Avro/ORC/Parquet，這是套用格式特定壓縮之前的大小。 將此屬性設定為未壓縮的資料大小（以位元組為單位），以提供原始資料大小。|
| `kustoTable` |  現有目標資料表的名稱。 覆寫分頁 `Table` 上的集合 `Data Connection` 。 |
| `kustoDataFormat` |  資料格式。 覆寫分頁 `Data format` 上的集合 `Data Connection` 。 |
| `kustoIngestionMappingReference` |  要使用之現有內嵌對應的名稱。 覆寫分頁 `Column mapping` 上的集合 `Data Connection` 。|
| `kustoIgnoreFirstRecord` | 如果設定為 `true` ，則 Kusto 會忽略 blob 的第一個資料列。 使用表格式格式資料 (CSV、TSV 或類似的) 略過標頭。 |
| `kustoExtentTags` | 代表將附加至結果範圍之 [標記](../kusto/management/extents-overview.md#extent-tagging) 的字串。 |
| `kustoCreationTime` |  覆寫 blob 的 [$IngestionTime](../kusto/query/ingestiontimefunction.md?pivots=azuredataexplorer) ，格式為 ISO 8601 字串。 用於回填。 |
