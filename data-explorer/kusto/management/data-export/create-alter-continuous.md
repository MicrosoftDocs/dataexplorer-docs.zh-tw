---
title: 建立或改變連續資料匯出-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中建立或改變連續資料匯出。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: b75ac4fe4b03c85eec74ccc69c5c03ff59a4bcfe
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149448"
---
# <a name="create-or-alter-continuous-export"></a>建立或改變連續匯出

建立或改變連續匯出作業。

## <a name="syntax"></a>語法

`.create-or-alter``continuous-export` *ContinuousExportName* <br>
[ `over` `(` *T1*， *T2* `)` ] <br>
`to``table` *ExternalTableName* <br> [ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ]<br>
\<| *查詢*

## <a name="properties"></a>屬性

| 屬性             | 類型     | 描述   |
|----------------------|----------|---------------------------------------|
| ContinuousExportName | String   | 連續匯出的名稱。 名稱在資料庫內必須是唯一的，而且可用來定期執行連續匯出。      |
| ExternalTableName    | String   | 要匯出的[外部資料表](../externaltables.md)名稱。  |
| 查詢                | String   | 要匯出的查詢。  |
|  (T1，T2)         | String   | 查詢中的選擇性事實資料表清單（以逗號分隔）。 如果未指定，查詢中參考的所有資料表都會假設為事實資料表。 如果指定，則*不*在此清單中的資料表會被視為維度資料表，而且不會進行範圍 (所有記錄都會參與所有的匯出) 。 如需詳細資訊，請參閱[連續資料匯出總覽](continuous-data-export.md)。 |
| intervalBetweenRuns  | Timespan | 連續匯出執行之間的時間範圍。 必須大於1分鐘。   |
| forcedLatency        | Timespan | 一段選擇性的時間，將查詢限制為僅在此期間之前內嵌的記錄 (相對於目前時間) 。 例如，如果查詢執行一些匯總/聯結，而您想要確保所有相關記錄都已經內嵌，然後再執行匯出，這個屬性就很有用。

除了上述以外，連續匯出 create 命令支援 [[匯出至外部資料表] 命令](export-data-to-an-external-table.md)所支援的所有屬性。 

## <a name="example"></a>範例

```kusto
.create-or-alter continuous-export MyExport
over (T)
to table ExternalBlob
with
(intervalBetweenRuns=1h, 
 forcedLatency=10m, 
 sizeLimit=104857600)
<| T
```

| 名稱     | ExternalTableName | 查詢 | ForcedLatency | IntervalBetweenRuns | CursorScopedTables         | ExportProperties                   |
|----------|-------------------|-------|---------------|---------------------|----------------------------|------------------------------------|
| MyExport | ExternalBlob      | S     | 00:10:00      | 01:00:00            | [<br>  "[' DB ']。[' S '] "<br>] | {<br>  "SizeLimit"：104857600<br>} |
