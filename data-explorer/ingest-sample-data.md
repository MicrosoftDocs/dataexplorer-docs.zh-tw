---
title: 將範例資料擷取至 Azure 資料總管
description: 了解如何將天氣相關範例資料擷取 (載入) 至「Azure 資料總管」。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 08/12/2019
ms.openlocfilehash: 3ece5a9d225e48654a0a3a96c3b7b78327565841
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81491913"
---
# <a name="quickstart-ingest-sample-data-into-azure-data-explorer"></a>快速入門：將範例資料擷取至 Azure 資料總管

本文說明如何將範例資料擷取 (載入) 至「Azure 資料總管」資料庫。 [數種擷取資料的方式有數種](ingest-data-overview.md)；本文將焦點放在適用於測試用途的基本方法。

> [!NOTE]
> 如果您已完成[使用 Azure 資料總管 Python 程式庫來擷取資料](python-ingest-data.md)，則您已經擁有此資料。

## <a name="prerequisites"></a>Prerequisites

[測試叢集和資料庫](create-cluster-database-portal.md)

## <a name="ingest-data"></a>擷取資料

**StormEvents** 範例資料集包含來自[美國國家環境資訊中心](https://www.ncdc.noaa.gov/stormevents/)的氣象相關資料。

1. 登入 [https://dataexplorer.azure.com](https://dataexplorer.azure.com)。

1. 在應用程式的左上方中，選取 [新增叢集]  。

1. 在 [新增叢集]  對話方塊中，以 `https://<ClusterName>.<Region>.kusto.windows.net/` 格式輸入您的叢集 URL，然後選取 [新增]  。

1. 貼上下列命令，然後選取 [執行]  以建立 StormEvents 資料表。

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```
1. 貼上下列命令，然後選取 [執行]  以將資料擷取至 StormEvents 資料表。

    ```Kusto
    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. 在擷取完成之後，貼上下列查詢，在視窗中選取該查詢，然後選取 [執行]  。

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    此查詢會從所擷取的範例資料傳回下列結果。

    ![查詢結果](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>後續步驟

* [Azure 資料總管資料擷取](ingest-data-overview.md)，以深入瞭解擷取方法。
* [快速入門：在 Azure 資料總管](web-query-data.md) Web UI 中查詢資料。
* 使用 Kusto 查詢語言[撰寫查詢](write-queries.md)。
