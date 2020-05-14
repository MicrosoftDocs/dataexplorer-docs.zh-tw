---
title: Kusto 查詢結果集超過內部限制-Azure 資料總管
description: 本文描述查詢結果集已超過內部 .。。Azure 資料總管中的限制。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 41a9851405ff85210bba7728a3737fc3b0a57f70
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382381"
---
# <a name="query-result-set-has-exceeded-the-internal--limit"></a>查詢結果集已超過內部 .。。只

*查詢結果集已超過內部 .。。「限制*」是一種[部分查詢失敗](partialqueryfailures.md)，會在查詢的結果超過兩個限制的其中一個時發生：
* 記錄數目的限制（ `record count limit` 預設會設定為500000）。
* 總數據量（ `data size limit` 預設設定為67108864（64mb））的限制。 

發生這種情況時，有幾個可能的動作課程：
* 變更查詢以耗用較少的資源。 例如，您可以嘗試限制查詢所傳回的記錄數目（使用[take 運算子](../query/takeoperator.md)或藉由加入其他[where 子句](../query/whereoperator.md)），或減少查詢所傳回的資料行數（使用[專案運算子](../query/projectoperator.md)或「[專案離開」運算子](../query/projectawayoperator.md)），或使用「[摘要」運算子](../query/summarizeoperator.md)來取得匯總的資料等。
* 暫時增加該查詢的相關查詢限制（請參閱 [[查詢限制](querylimits.md)] 底下的 [**結果截斷**]）。  
  請注意，一般而言，這不是建議的作法，因為它的限制是為了確保單一查詢不會中斷在叢集上執行的並行查詢。