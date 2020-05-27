---
title: 開始使用 Kusto
description: 本文說明如何開始使用 Kusto。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0a56878c8b79e651afcd1b8ce18a3220dc304211
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863382"
---
# <a name="getting-started-with-kusto"></a>開始使用 Kusto

Azure 資料總管是一種服務，可用於儲存和執行巨量資料的互動式分析。

其是以關係資料庫管理系統為基礎，支援資料庫、資料表和資料行之類的實體。 複雜的分析查詢會使用 Kusto 查詢語言來執行。 某些查詢運算子包含計算結果欄、搜尋和篩選或資料列、依彙總分組，以及聯結。

服務藉由「犧牲」執行個別資料列和跨資料表條件約束/交易的就地更新功能，提供絕佳的資料內嵌和查詢效能。 因此，其會代替 (而不是取代) OLTP 和資料倉儲處理等案例的傳統 RDBMS 系統。

結構化、半結構化 (類似 JSON 的巢狀類型) 和非結構化 (自然語言) 資料的處理方式都一樣。

## <a name="interacting-with-azure-data-explorer"></a>與 Azure 資料總管互動

使用者與 Kusto 互動的主要方式是使用可用的許多[用戶端工具](../tools/index.md)之一。 雖然支援 [SQL 查詢](../api/tds/t-sql.md)，但互動的主要方式是透過使用 [Kusto 查詢語言](../query/index.md)來傳送資料查詢，以及透過使用[控制命令](../management/index.md)來管理實體、探索中繼資料等。查詢和控制命令基本上都是簡短的文字「程式」。

## <a name="kusto-queries"></a>Kusto 查詢

查詢是唯讀要求，用於處理資料並傳回處理結果，而不需修改資料或中繼資料。 Kusto 查詢可以使用 [SQL 語言](../api/tds/t-sql.md)或 [Kusto 查詢語言](../query/index.md)。 以下查詢是後者的範例，其會計算 `Logs` 資料表中有多少資料列的 `Level` 資料行值等於字串 `Critical`：

```kusto
Logs
| where Level == "Critical"
| count
```

查詢的開頭不能是點 (`.`) 字元或井號 (`#`) 字元。

## <a name="control-commands"></a>控制命令

控制命令是要求 Kusto 處理且可能修改資料或中繼資料。 例如，下列控制命令會建立包含兩個資料行 `Level` 和 `Text` 的新 Kusto 資料表：

```kusto
.create table Logs (Level:string, Text:string)
```

控制命令有自己的語法 (這不屬於 Kusto 查詢語言語法，雖然兩者共用許多概念)。 尤其是，讓命令文字中的第一個字元是點 (`.`) 字元 (無法啟動查詢)，控制命令就會與查詢有所區別。
此區別可防止許多種類的安全性攻擊，只是因為這可避免在查詢中內嵌控制命令。

並非所有控制命令都會修改資料或中繼資料。 一個大型命令類別，以 `.show` 開頭的命令會用來顯示中繼資料或資料。 例如，`.show tables` 命令會傳回目前資料庫中所有資料表的清單。
