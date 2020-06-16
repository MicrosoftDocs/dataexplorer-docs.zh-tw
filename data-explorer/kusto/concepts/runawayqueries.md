---
title: 失控查詢-Azure 資料總管
description: 本文說明 Azure 資料總管中失控的查詢。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 8300289cb98e2f23613c15a711982efe06f327cf
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780247"
---
# <a name="runaway-queries"></a>失控查詢

*失控查詢*是一種[部分查詢失敗](partialqueryfailures.md)，會在查詢執行期間超過一些內部[查詢限制](querylimits.md)時發生。 

例如，可能會回報下列錯誤：`HashJoin operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete.`

有幾個可能的動作課程。
* 變更查詢以耗用較少的資源。 例如，如果錯誤指出查詢結果集太大，您可以：
  * 限制查詢所傳回的記錄數目
     * 使用[take 運算子](../query/takeoperator.md)
     * 加入額外[的 where 子句](../query/whereoperator.md)
  * 減少由查詢傳回的資料行數目 
     * 使用[專案運算子](../query/projectoperator.md)
     * 使用「[專案離開」運算子](../query/projectawayoperator.md)
  * 使用「[摘要」運算子](../query/summarizeoperator.md)來取得匯總資料。
* 暫時增加該查詢的相關查詢限制。 如需詳細資訊，請參閱[查詢限制-每個反覆運算器的記憶體限制](querylimits.md)。 不過，不建議使用這個方法。 有限制可保護叢集，並確保單一查詢不會中斷在叢集上執行的並行查詢。
