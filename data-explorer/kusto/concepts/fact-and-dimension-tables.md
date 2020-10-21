---
title: 事實和維度資料表-Azure 資料總管
description: 本文說明 Azure 資料總管中的事實和維度資料表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 608d177d555419f8e2340ddacffd32382118eb7f
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92343295"
---
# <a name="fact-and-dimension-tables"></a>事實和維度資料表

當您為 Azure 資料總管資料庫設計架構時，請將資料表想像成廣泛屬於兩個類別的其中一種。
* [事實資料表](https://en.wikipedia.org/wiki/Fact_table)
* [維度資料表](https://en.wikipedia.org/wiki/Dimension_(data_warehouse)#Dimension_table)

## <a name="fact-tables"></a>事實資料表
事實資料表是指其記錄為不可變「事實」的資料表，例如服務記錄和度量資訊。 記錄會以串流方式或大型區塊逐漸附加到資料表中。 記錄會一直留在那裡，直到它們因為成本而移除為止，或是因為它們遺失其價值。 否則永遠不會更新記錄。

實體資料有時會保留在事實資料表中，而實體資料的變更會變慢。 例如，某些實體實體的相關資料，例如不常變更位置的一小部分辦公室設備。
因為 Kusto 中的資料是不可變的，所以常見的作法是讓每個資料表保存兩個數據行：
* 識別實體 () 資料行的身分識別 `string`
* 上次修改的 (`datetime`) 時間戳記資料行

然後，只會抓取每個實體身分識別的最後一筆記錄。

## <a name="dimension-tables"></a>維度資料表
維度資料表：
* 保存參考資料，例如實體識別碼至其屬性的查閱資料表
* 在單一交易中整個內容變更的資料表中保存類似快照集的資料

維度資料表不會定期內嵌新資料。 相反地，整個資料內容會一次更新一次，使用的作業如 [： set-或-replace](../management/data-ingestion/ingest-from-query.md)、 [. move 範圍](../management/move-extents.md)或 [。重新命名資料表](../management/rename-table-command.md)。

有時候，維度資料表可能衍生自事實資料表。 您可以透過事實資料表上的 [更新原則](../management/updatepolicy.md) 來完成此程式，並在資料表上進行查詢，以取得每個實體的最後一筆記錄。

## <a name="differentiate-fact-and-dimension-tables"></a>區分事實和維度資料表

Kusto 中有一些可區分事實資料表和維度資料表的程式。 其中一個是 [連續匯出](../management/data-export/continuous-data-export.md)。

這些機制保證可以精確地處理事實資料表中的資料一次。 它們依賴 [資料庫資料指標](../management/databasecursor.md) 機制。

例如，每次執行連續匯出作業時，都會匯出上次更新資料庫資料指標之後所內嵌的所有記錄。 連續匯出作業必須區分事實資料表和維度資料表。 事實資料表只會處理新內嵌的資料，而維度資料表會用來做為查閱。 因此，整個資料表都必須列入考慮。

沒有任何方法可將資料表「標示」為「事實資料表」或「維度資料表」。
資料在資料表中的內嵌方式，以及資料表的使用方式，是識別其類型的方式。