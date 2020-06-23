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
ms.openlocfilehash: 6db37366ddd3d70aaa89c0d6eebd1ec8affbb76d
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264436"
---
# <a name="fact-and-dimension-tables"></a>事實和維度資料表

在設計 Azure 資料總管資料庫的架構時，請將資料表視為屬於兩個類別的其中一個。
* [事實資料表](https://en.wikipedia.org/wiki/Fact_table)
* [維度資料表](https://en.wikipedia.org/wiki/Dimension_(data_warehouse)#Dimension_table)

## <a name="fact-tables"></a>事實資料表
事實資料表是一種資料表，其記錄為不可變的「事實」，例如服務記錄和度量資訊。 記錄會以資料流程方式或大型區塊逐漸附加到資料表中。 記錄會保留在那裡，直到其因為成本或遺失其值而被移除為止。 否則永遠不會更新記錄。

實體資料有時會保留在事實資料表中，實體資料的變更速度會變慢。 例如，某些實體實體的相關資料，例如不常變更位置的辦公室設備。
由於 Kusto 中的資料是不可變的，因此常見的做法是讓每個資料表都包含兩個數據行：
* 識別實體的識別（ `string` ）資料行
* 上次修改 `datetime` 時間戳記資料行

然後只會抓取每個實體身分識別的最後一筆記錄。

## <a name="dimension-tables"></a>維度資料表
維度資料表：
* 保存參考資料，例如從實體識別碼到其屬性的查閱資料表
* 在單一交易中整個內容變更的資料表中保存類似快照集的資料

維度資料表不會定期內嵌新資料。 相反地，會使用諸如[. set-或-replace](../management/data-ingestion/ingest-from-query.md)、 [. move 範圍](../management/extents-commands.md#move-extents)或[. rename 資料表](../management/rename-table-command.md)的作業，一次更新整個資料內容。

有時候，維度資料表可能是從事實資料表衍生而來。 此程式可透過事實資料表上的[更新原則](../management/updatepolicy.md)來完成，並在資料表上查詢以取得每個實體的最後一筆記錄。

## <a name="differentiate-fact-and-dimension-tables"></a>區分事實和維度資料表

Kusto 中的進程會區分事實資料表和維度資料表。 其中一個是[連續匯出](../management/data-export/continuous-data-export.md)。

這些機制保證能夠精確地處理事實資料表中的資料一次。 它們會依賴[資料庫資料指標](../management/databasecursor.md)機制。

例如，每次執行連續匯出作業時，都會匯出上次更新資料庫資料指標之後所內嵌的所有記錄。 連續匯出作業必須區分事實資料表和維度資料表。 事實資料表只會處理新內嵌的資料，並使用維度資料表做為查閱。 因此，整個資料表都必須列入考慮。

沒有任何方法可以將資料表「標示」為「事實資料表」或「維度資料表」。
將資料內嵌到資料表中的方式，以及如何使用資料表，就是識別其類型的方法。
