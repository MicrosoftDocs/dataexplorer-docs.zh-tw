---
title: 'ingestion_time ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 ingestion_time。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: ba4429639f7c4775eab34797b7d7ff04fa247482
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252269"
---
# <a name="ingestion_time"></a>ingestion_time()

::: zone pivot="azuredataexplorer"

傳回內嵌目前記錄的大約時間。

此函數必須用於內嵌資料之資料表的內容，而該資料表已在內嵌資料時啟用 [IngestionTime 原則](../management/ingestiontimepolicy.md) 。 否則，此函式會產生 null 值。

::: zone-end

::: zone pivot="azuremonitor"

在 `datetime` 內嵌記錄並準備進行查詢時，抓取。

::: zone-end

> [!NOTE]
> 此函數所傳回的值只是近似值，因為內嵌程式可能需要幾分鐘的時間才能完成，而且可能會同時進行多個內嵌活動。 若要使用剛好一次的保證來處理資料表的所有記錄，請使用 [資料庫資料指標](../management/databasecursor.md)。

## <a name="syntax"></a>語法

`ingestion_time()`

## <a name="returns"></a>傳回

`datetime`值，指定要內嵌到資料表中的大約時間。

## <a name="example"></a>範例

```kusto
T
| extend ingestionTime = ingestion_time() | top 10 by ingestionTime
```
