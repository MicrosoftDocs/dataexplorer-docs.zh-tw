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
ms.openlocfilehash: 9706cce08a99989441aa657c5d85465294be837e
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512413"
---
# <a name="query-result-set-has-exceeded-the-internal--limit"></a>查詢結果集已超過內部 .。。只

*查詢結果集已超過內部 .。。「限制*」是一種[部分查詢失敗](partialqueryfailures.md)，會在查詢的結果超過兩個限制的其中一個時發生：
* 記錄數目的限制（ `record count limit` 預設設定為500000）
* 總數據量（ `data size limit` ，預設設定為67108864（64mb））的限制

有幾個可能的動作課程：

* 變更查詢以耗用較少的資源。 
  例如，您可以：
  * 使用[take 運算子](../query/takeoperator.md)或加入額外[的 where 子句](../query/whereoperator.md)，來限制查詢所傳回的記錄數目
  * 請嘗試減少查詢所傳回的資料行數目。 使用[專案運算子](../query/projectoperator.md)或「[專案離開」運算子](../query/projectawayoperator.md)）
  * 使用[摘要運算子](../query/summarizeoperator.md)取得匯總資料
* 暫時增加該查詢的相關查詢限制。 如需詳細資訊，請參閱[查詢限制](querylimits.md)下的**結果截斷**）

 > [!NOTE] 
 > 我們不建議您增加查詢限制，因為有限制可以保護叢集。 這些限制可確保單一查詢不會干擾在叢集上執行的並行查詢。
  
