---
title: 建立或改變持續資料匯出-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中建立或改變連續資料匯出。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: 11822a58b2ced0b257ad649997bc154ab4be19d5
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337579"
---
# <a name="create-or-alter-continuous-export"></a>建立或改變連續匯出

建立或改變連續匯出工作。

## <a name="syntax"></a>語法

`.create-or-alter``continuous-export` *ContinuousExportName* <br>
[ `over` `(` *T1*， *T2* `)` ] <br>
`to``table` *ExternalTableName* <br> [ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ]<br>
\<| *查詢*

## <a name="properties"></a>屬性

| 屬性             | 類型     | 說明   |
|----------------------|----------|---------------------------------------|
| ContinuousExportName | String   | 連續匯出的名稱。 名稱在資料庫內必須是唯一的，並且用來定期執行連續匯出。      |
| ExternalTableName    | String   | 要匯出的 [外部資料表](../external-table-commands.md) 名稱。  |
| 查詢                | String   | 要匯出的查詢。  |
| over (T1，T2)         | String   | 查詢中事實資料表的選擇性逗號分隔清單。 如果未指定，則會假設查詢中參考的所有資料表都是事實資料表。 如果指定，則 *不* 在此清單中的資料表會被視為維度資料表，而且不會設定範圍 (所有記錄都會參與所有匯出) 。 如需詳細資料，請參閱 [持續資料匯出總覽](continuous-data-export.md) 。 |
| intervalBetweenRuns  | Timespan | 連續匯出執行之間的時間範圍。 必須大於1分鐘。   |
| forcedLatency        | Timespan | 一段選擇性的時間，可將查詢限制為僅在此期間內內嵌的記錄， (相對於目前的時間) 。 例如，如果查詢執行一些匯總/聯結，而且您想要確定在執行匯出之前已內嵌所有相關的記錄，則這個屬性會很有用。

除了上述屬性外，「 [匯出至外部資料表」命令](export-data-to-an-external-table.md) 中支援的所有屬性都可在「連續匯出建立」命令中受到支援。 

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
| MyExport | ExternalBlob      | S     | 00:10:00      | 01:00:00            | [<br>  "[' DB ']。[的 '] "<br>] | {<br>  "SizeLimit"：104857600<br>} |