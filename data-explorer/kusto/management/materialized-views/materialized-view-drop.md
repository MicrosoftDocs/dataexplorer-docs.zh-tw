---
title: 具體化視圖卸載-Azure 資料總管
description: 本文說明 Azure 資料總管中的 drop 具體化 view 命令。
services: data-explorer
author: orspod
ms.author: yifats
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: 312b8dbd15f9ee570d1693f7bdbb77b9988d8207
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320602"
---
# <a name="drop-materialized-view"></a>.drop materialized-view 

卸載具體化視圖。

需要 [資料庫管理員](../access-control/role-based-authorization.md) 或具體化 view Admin 許可權。

## <a name="syntax"></a>語法

`.drop``materialized-view` *MaterializedViewName*

## <a name="properties"></a>屬性

| 屬性 | 類型| 描述 |
|----------------|-------|-----|
| MaterializedViewName| String| 具體化視圖的名稱。|

## <a name="returns"></a>傳回

此命令會傳回資料庫中的其餘具體化視圖，也就是 [顯示具體化 view](materialized-view-show-commands.md#show-materialized-view) 命令的輸出。

## <a name="example"></a>範例

```kusto
.drop materialized-view ViewName
```

## <a name="output"></a>輸出

|輸出參數 |類型 |描述
|---|---|---|
|Name  |String |具體化視圖的名稱。
|SourceTable|String|具體化視圖的來源資料表。
|查詢|String|具體化 view 查詢。
|MaterializedTo|Datetime|來源資料表中 ( # A1 時間戳記的最大具體化 ingestion_time。 如需詳細資訊，請參閱 [具體化視圖的運作方式](materialized-view-overview.md#how-materialized-views-work)。
|LastRun|Datetime |上次具體化的執行時間。
|LastRunResult|String|上次執行的結果。 傳回 `Completed` 成功的執行，否則為 `Failed` 。
|IsHealthy|bool|`True` 當 view 視為狀況良好時，則為， `False` 否則為。 如果成功具體化到最後一個小時，就會將 View 視為狀況良好， (`MaterializedTo` 大於 `ago(1h)`) 。
|IsEnabled|bool|`True` 啟用 view 時 (參閱 [停用或啟用具體化 view](materialized-view-enable-disable.md)) 。
|資料夾|字串|具體化 view 資料夾。
|DocString|字串|具體化 view doc 字串。
|AutoUpdateSchema|bool|是否已啟用自動更新的視圖。
|EffectiveDateTime|Datetime|視圖的有效日期時間（于建立期間決定） (查看 [`.create materialized-view`](materialized-view-create.md#create-materialized-view)) 
