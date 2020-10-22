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
ms.openlocfilehash: 448da9db3e515385ca44cec94d9dd8604a8c1125
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356566"
---
# <a name="query-result-set-has-exceeded-the-internal--limit"></a>查詢結果集已超過內部 .。。限制

*查詢結果集已超過內部 .。。limit*是一種[部分查詢失敗](partialqueryfailures.md)，當查詢的結果超過兩個限制的其中一個時，就會發生這種失敗：
* 記錄數目的限制 (`record count limit` ，預設為 500000) 
*  (的總數據量限制 `data size limit` ，預設為 67108864 (64mb) # A3

有幾個可能的動作課程：

* 變更查詢以耗用較少的資源。 
  例如，您可以：
  * 使用[take 運算子](../query/takeoperator.md)或新增其他[where 子句](../query/whereoperator.md)來限制查詢所傳回的記錄數目
  * 請嘗試減少查詢所傳回的資料行數目。 使用 [專案運算子](../query/projectoperator.md)、 [專案離開運算子](../query/projectawayoperator.md)或 [專案-保留運算子](../query/project-keep-operator.md)
  * 使用 [摘要運算子](../query/summarizeoperator.md) 來取得匯總資料
* 暫時增加該查詢的相關查詢限制。 如需詳細資訊，請參閱[查詢限制](querylimits.md)下的**結果截斷**) 

 > [!NOTE] 
 > 我們不建議您提高查詢限制，因為有限制可保護叢集。 這些限制可確保單一查詢不會中斷叢集中執行的並行查詢。
  
