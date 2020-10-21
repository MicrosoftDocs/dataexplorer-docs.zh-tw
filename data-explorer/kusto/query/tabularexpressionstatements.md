---
title: 表格式運算式語句-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的表格式運算式語句。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 943281c2e06aecfffab72ca0f98597c19938d936
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250755"
---
# <a name="tabular-expression-statements"></a>表格式運算式陳述式

使用表格式運算式語句時，人們通常會在談到查詢時記住。 這個語句通常會出現在語句清單中的最後一個，而且其輸入和輸出都是由資料表或表格式資料集所組成。

Kusto 使用表格式運算式語句的資料流程模型。 表格式運算式語句的一般結構是 *表格式資料來源* 的組成 (例如，Kusto 資料表) 、 *表格式資料運算子* (例如篩選和預測) ，以及可能的轉譯 *運算子*。 組合是以「管道字元」 (`|`) ，為語句提供非常一般的表單，以視覺方式表示從左至右的表格式資料流程程。
每個運算子都會接受「來自直立線」的表格式資料集，以及來自運算子主體的其他輸入 (包括其他表格式資料集)，然後將表格式資料集發出至緊接在後的下一個運算子：   

*source1.rc* `|`*operator1* `|`*operator2* `|`*renderInstruction*

在下列範例中，來源是 `Logs` (目前資料庫中資料表的參考) ，第一個運算子會 `where` (根據某些個別) 記錄述詞來篩選出其輸入的記錄，而第二個運算子則是 `count` 在其輸入資料集 (中計算記錄數目的) ：

```kusto
Logs | where Timestamp > ago(1d) | count
```

在下列更複雜的範例中， `join` 運算子用來結合兩個輸入資料集的記錄：一個是資料表上的篩選 `Logs` ，另一個是資料表上的篩選 `Events` 。

```kusto
Logs 
| where Timestamp > ago(1d) 
| join 
(
    Events 
    | where continent == 'Europe'
) on RequestId 
```

## <a name="tabular-data-sources"></a>表格式資料來源

**表格式資料來源**會產生一組記錄，供**表格式資料運算子**進一步處理。 Kusto 支援下列其中一些來源：

* 資料表參考 (參考內容資料庫或其他叢集/資料庫中的 Kusto 資料表。 ) 
* 表格式 [範圍運算子](rangeoperator.md)。
* [Print 運算子](printoperator.md)。
* 傳回資料表之函數的調用。
* [資料表常](datatableoperator.md)值 ( "datatable" ) 。