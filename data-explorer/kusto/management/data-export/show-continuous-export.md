---
title: 顯示連續資料匯出-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中顯示連續資料匯出屬性。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: db7583c055468808ade1166c0bfee90859399d32
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149414"
---
# <a name="show-continuous-export"></a>顯示連續匯出

傳回*ContinuousExportName*的連續匯出屬性。 

## <a name="syntax"></a>語法

`.show``continuous-export` *ContinuousExportName*

## <a name="properties"></a>屬性

| 屬性             | 類型   | 描述                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 連續匯出的名稱。 |

`.show` `continuous-exports`

傳回資料庫中的所有連續匯出。 

## <a name="output"></a>輸出

| 輸出參數    | 類型     | 描述                                                             |
|---------------------|----------|-------------------------------------------------------------------------|
| CursorScopedTables  | String   | 明確限定範圍 (事實) 資料表的清單 (JSON 序列化)                |
| ExportProperties    | String   |  (JSON 序列化) 匯出屬性                                     |
| ExportedTo          | Datetime | 最後一個日期時間 (已成功匯出的內嵌時間)        |
| ExternalTableName   | String   | 外部資料表的名稱                                              |
| ForcedLatency       | TimeSpan | 強制延遲 (null （如果未提供）)                                    |
| IntervalBetweenRuns | TimeSpan | 執行之間的間隔                                                   |
| IsDisabled          | Boolean  | 如果連續匯出已停用，則為 True                               |
| IsRunning           | Boolean  | 如果連續匯出目前正在執行，則為 True                      |
| LastRunResult       | String   | 上次連續匯出執行的結果 (`Completed` 或 `Failed`)  |
| LastRunTime         | Datetime | 上次執行連續匯出的時間 (開始時間)            |
| 名稱                | String   | 連續匯出的名稱                                           |
| 查詢               | String   | 匯出查詢                                                            |
| StartCursor         | String   | 第一次執行此連續匯出的起點         |

