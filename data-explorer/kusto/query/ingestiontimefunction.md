---
title: 'ingestion_time ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 ingestion_time ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: fea923b0d917beb505bd6a1cb9ee1339739d08c6
ms.sourcegitcommit: 284152eba9ee52e06d710cc13200a80e9cbd0a8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2020
ms.locfileid: "86291554"
---
# <a name="ingestion_time"></a>ingestion_time()

::: zone pivot="azuredataexplorer"

傳回目前記錄內嵌的大約時間。

此函式必須用於內嵌資料之資料表的內容中，而[IngestionTime 原則](../management/ingestiontimepolicy.md)是在內嵌資料時啟用。 否則，此函數會產生 null 值。

::: zone-end

::: zone pivot="azuremonitor"

`datetime`當記錄已內嵌且準備好進行查詢時，會抓取。

::: zone-end

> [!NOTE]
> 此函式所傳回的值只是近似值，因為內嵌程式可能需要幾分鐘的時間才能完成，而且可能會同時進行多個內嵌活動。 若要處理具有剛好一次保證之資料表的所有記錄，請使用[資料庫資料指標](../management/databasecursor.md)。

**語法**

`ingestion_time()`

**傳回**

`datetime`值，指定在資料表中內嵌的大約時間。

**範例**

```kusto
T
| extend ingestionTime = ingestion_time() | top 10 by ingestionTime
```
