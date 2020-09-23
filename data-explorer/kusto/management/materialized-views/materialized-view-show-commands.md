---
title: 顯示具體化視圖命令-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中顯示具體化視圖命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: 4a9b42410c7a64a54ced0dc326b33242b4b11870
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057098"
---
# <a name="show-materialized-views-commands"></a>。顯示具體化-views 命令

下列顯示命令會顯示有關 [具體化視圖](materialized-view-overview.md)的資訊。

## <a name="show-materialized-view"></a>。顯示具體化-view

顯示具體化視圖的定義及其目前狀態的相關資訊。

### <a name="syntax"></a>語法

`.show``materialized-view` *MaterializedViewName*

`.show` `materialized-views`

### <a name="properties"></a>屬性

|屬性|類型|描述
|----------------|-------|---|
|MaterializedViewName|String|具體化視圖的名稱。|

### <a name="example"></a>範例

```kusto
.show materialized-view ViewName
```

### <a name="output"></a>輸出

|輸出參數 |類型 |描述
|---|---|---
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
|EffectiveDateTime|Datetime|視圖的有效日期時間（于建立期間決定） (參閱 [。建立具體化視圖](materialized-view-create.md#create-materialized-view)) 。

## <a name="show-materialized-view-schema"></a>。顯示具體化-view 架構

傳回 CSL/JSON 中具體化視圖的架構。

### <a name="syntax"></a>語法

`.show``materialized-view` *MaterializedViewName*`cslschema`

`.show``materialized-view` *MaterializedViewName* `schema` MaterializedViewName `as``json`

`.show``materialized-view` *MaterializedViewName* `schema` MaterializedViewName `as``csl`

### <a name="output-parameters"></a>輸出參數

| 輸出參數 | 類型   | 描述                                               |
|------------------|--------|-----------------------------------------------------------|
| TableName        | String | 具體化視圖的名稱。                        |
| 結構描述           | String | 具體化 view csl 架構                          |
| DatabaseName     | String | 具體化視圖所屬的資料庫       |
| 資料夾           | String | 具體化視圖的資料夾                                |
| DocString        | String | 具體化視圖的 docstring                             |

## <a name="show-materialized-view-extents"></a>。顯示具體化-view 範圍

傳回具體化視圖 *具體化* 部分中的範圍。 如需 *具體化* 部分的定義，請參閱 [具體化視圖的運作方式](materialized-view-overview.md#how-materialized-views-work)。

此命令會提供與 [ [顯示資料表範圍](../show-extents.md#table-level) ] 命令相同的詳細資料。

### <a name="syntax"></a>語法

`.show``materialized-view` *MaterializedViewName* `extents` [ `hot` ]
 
## <a name="show-materialized-view-failures"></a>。顯示具體化-view 失敗

傳回具體化視圖具體化進程中發生的失敗。

### <a name="syntax"></a>語法

`.show``materialized-view` *MaterializedViewName*`failures`

### <a name="properties"></a>屬性

|屬性|類型|描述
|----------------|-------|---|
|MaterializedViewName|String|具體化視圖的名稱。|

### <a name="output"></a>輸出

|輸出參數 |類型 |描述
|---|---|---
|名稱  |時間戳記 |失敗時間戳記。
|OperationId  |String |執行失敗的作業 ID。
|Name|String|具體化視圖名稱。
|LastSuccessRun|Datetime|最後一次執行成功完成的時間戳記。
|FailureKind|String|失敗的類型。
|詳細資料|String|失敗詳細資料。

