---
title: 在 Azure 資料總管中處理重複的資料
description: 本主題將說明使用 Azure 資料總管時處理重復資料的各種方法。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: how-to
ms.date: 12/19/2018
ms.openlocfilehash: fd277cd46a183606e35219f733dbf86b094d62f8
ms.sourcegitcommit: 4b061374c5b175262d256e82e3ff4c0cbb779a7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2020
ms.locfileid: "94373794"
---
# <a name="handle-duplicate-data-in-azure-data-explorer"></a>在 Azure 資料總管中處理重複的資料

將資料傳送至雲端的裝置會維持資料的本機快取。 根據資料大小，本機快取可能會將資料儲存數天或甚至數個月。 您想要保護分析資料庫，以免受到會重新傳送快取資料並造成分析資料庫中資料重複的故障裝置影響。 本主題概述處理這幾種情況下重複資料的最佳做法。

資料重複的最佳解決方案就是防止重複。 可能的話，修正稍早的資料管線問題，以節省與順著資料管線移動資料相關聯的成本，並可避免將資源用於處理內嵌至系統中的重複資料。 不過，在來源系統無法修改的情況下，有各種方式可處理這種情況。

## <a name="understand-the-impact-of-duplicate-data"></a>了解重複資料的影響

監視重複資料的百分比。 發現重複資料的百分比後，您可以分析問題和業務影響範圍，並選擇適當的解決方案。

可識別重複記錄百分比的範例查詢：

```kusto
let _sample = 0.01; // 1% sampling
let _data =
DeviceEventsAll
| where EventDateTime between (datetime('10-01-2018 10:00') .. datetime('10-10-2018 10:00'));
let _totalRecords = toscalar(_data | count);
_data
| where rand()<= _sample
| summarize recordsCount=count() by hash(DeviceId) + hash(EventId) + hash(StationId)  // Use all dimensions that make row unique. Combining hashes can be improved
| summarize duplicateRecords=countif(recordsCount  > 1)
| extend duplicate_percentage = (duplicateRecords / _sample) / _totalRecords  
```

## <a name="solutions-for-handling-duplicate-data"></a>適用於處理重複資料的解決方案

### <a name="solution-1-dont-remove-duplicate-data"></a>解決方案 #1：不移除重複的資料

了解您的業務需求和重複資料的容限。 有些資料集可以管理重複資料的百分比。 如果重複資料並沒有重大影響，您可以忽略它的存在。 不移除重複資料的優點就是沒有額外的擷取程序或查詢效能負荷。

### <a name="solution-2-handle-duplicate-rows-during-query"></a>方案 #2：在查詢期間處理重複的資料列

另一個選項是在查詢期間篩選出資料中重複的資料列。 [`arg_max()`](kusto/query/arg-max-aggfunction.md)彙總函式可用來篩選出重複的記錄，並根據時間戳記 (或另一個資料行) 來傳回最後一筆記錄。 使用此方法的優點是擷取速度更快，因為查詢期間會發生重複資料刪除。 此外，所有記錄 (包括重複項目) 均可供稽核和進行疑難排解。 使用 `arg_max` 函式的缺點是每次查詢資料時有額外的查詢時間和 CPU 負載。 視所查詢的資料量而定，此解決方案可能會變得無法運作或耗用記憶體，而且需要切換至其他選項。

在下列範例中，我們會查詢針對一組可判斷唯一記錄的資料行內嵌的最後一筆記錄：

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

此查詢也可以放在函式內部，而不是直接查詢資料表：

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>解決方案 #3：在內嵌進程期間篩選重複專案

另一個解決方案是在擷取程序期間篩選重複項目。 系統會在內嵌 Kusto 資料表時忽略重複的資料。 移除重複的資料列之後，資料會內嵌到暫存資料表中，並複製到另一個資料表。 此解決方案的優點是相較於先前的解決方案，查詢效能大幅改善。 缺點包括增加的擷取時間和其他資料儲存體成本。 此外，此解決方案只適用于重複問題不會同時內嵌的情況。 如果有多個並行內嵌包含重複的記錄，則可能會內嵌所有資料，因為重復資料刪除程式在資料表中找不到任何現有的相符記錄。    

下列範例可說明此方法：

1. 建立另一個具有相同結構描述的資料表：

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. 藉由反聯結新的記錄與先前內嵌的記錄，建立函式來篩選出重複的記錄。

    ```kusto
    .create function RemoveDuplicateDeviceEvents()
    {
    DeviceEventsAll
    | join hint.strategy=broadcast kind = anti
        (
        DeviceEventsUnique
        | where EventDateTime > ago(7d)   // filter the data for certain time frame
        | limit 1000000   //set some limitations (few million records) to avoid choking-up the system during outage recovery

        ) on DeviceId, EventId, StationId
    }
    ```

    > [!NOTE]
    > 聯結 CPU 傾向作業，並且在系統上新增額外的負載。

1. 在 `DeviceEventsUnique` 資料表上設定[更新原則](kusto/management/update-policy.md)。 當新資料進入 `DeviceEventsAll` 資料表時，就會啟用更新原則。 Kusto 引擎會在建立新的[範圍](kusto/management/extents-overview.md)時自動執行函式。 處理範圍為新建立的資料。 下列命令會將來源資料表 (`DeviceEventsAll`)、目的地資料表 (`DeviceEventsUnique`) 和函式 `RemoveDuplicatesDeviceEvents` 拼接一起，以建立更新原則。

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > 更新原則會在擷取期間延長資料篩選後的擷取持續時間，然後內嵌兩次 (內嵌到 `DeviceEventsAll` 資料表和 `DeviceEventsUnique` 資料表)。

1. (選擇性) 在 `DeviceEventsAll` 資料表上設定較低的資料保留期，以避免儲存資料的複本。 根據您要保留的資料量和時間長度，選擇天數，以便進行疑難排解。 您可以將它設定為 `0d` 保留天數，以節省 COGS (銷售的商品成本) 並改善效能，因為資料不會上傳至儲存體。

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>摘要

有很多種方式可以處理資料重複情形。 仔細評估這些選項，將價格和效能納入考量，為您的企業決定正確的方法。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [撰寫 Azure 資料總管的查詢](write-queries.md)
