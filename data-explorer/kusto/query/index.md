---
title: 總覽 - Azure 資料總管
description: 本文說明 Azure 資料總管的概觀。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/07/2019
ms.localizationpriority: high
adobe-target: true
ms.openlocfilehash: d0f71416410812b8160354781111f4a6f46350fd
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2021
ms.locfileid: "100359687"
---
# <a name="overview"></a>概觀

Kusto 查詢是一種負責處理資料並傳回結果的唯讀要求。
該要求是採用針對讓語法易於理解、撰寫及自動執行所設計的資料流程模型，並採用純文字加以陳述。 此查詢使用的結構描述實體組織架構類似於 SQL 的階層：資料庫、資料表與資料欄。

此查詢包含一系列以分號 (`;`) 分隔的查詢陳述式，其中至少有一個陳述式為[表格式運算陳述式](tabularexpressionstatements.md)，這種陳述式所產生的資料會以類似表格之網格的資料行與資料列方式排列。 此查詢的表格式運算陳述式會產生查詢的結果。

此表格式運算陳述式語法中的表格式資料會從某個表格式查詢運算子流到另一個運算子，先從資料來源開始 (例如，資料庫中的資料表，或是會產生資料的運算子)，然後流經一組使用管線運算子 (`|`) 分隔符號繫結在一起的資料轉換運算子。

例如，下列 Kusto 查詢會有一個陳述式，亦即表格式運算陳述式。 此陳述式一開始會先參照名為 `StormEvents` 的資料表 (裝載此資料表的資料庫在此是隱含的，且為連接資訊的一部分)。 接著會篩選資料表的資料 (資料列)，先依 `StartTime` 資料行的值篩選，再以 `State` 資料行的值篩選。 然後查詢會傳回「仍正常運作」的資料列計數。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where StartTime >= datetime(2007-11-01) and StartTime < datetime(2007-12-01)
| where State == "FLORIDA"  
| count 
```

若要執行此查詢，請[按一下這裡](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAwsuyS/KdS1LzSspVuDlqlEoz0gtSlUILkksKgnJzE1VsLNVSEksSS0BsjWMDAzMdQ0NdQ0MNRUS81KQVNmgKzICKUIxryRVwdZWQcnNxz/I08VRSQFsW3J+aV6JAgAwMx4+hAAAAA==)。
在此案例中，結果會是：

|Count|
|-----|
|   23|
