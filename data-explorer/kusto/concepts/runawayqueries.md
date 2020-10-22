---
title: 失控查詢-Azure 資料總管
description: 本文說明 Azure 資料總管中的失控查詢。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4f2cf559863eecbd267855cd4a22f2dbe8673dc7
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356600"
---
# <a name="runaway-queries"></a>失控查詢

*失控查詢*是一種[部分查詢失敗](partialqueryfailures.md)，在查詢執行期間超過部分內部[查詢限制](querylimits.md)時，就會發生這種錯誤。 

例如，可能會回報下列錯誤： `HashJoin operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete.`

有幾個可能的動作課程。
* 變更查詢以耗用較少的資源。 例如，如果錯誤指出查詢結果集太大，您可以：
  * 限制查詢傳回的記錄數目
     * 使用 [take 運算子](../query/takeoperator.md)
     * 加入其他 [where 子句](../query/whereoperator.md)
  * 減少查詢傳回的資料行數目
     * 使用 [專案運算子](../query/projectoperator.md)
     * 使用 [專案離開運算子](../query/projectawayoperator.md)
     * 使用 [專案保留運算子](../query/project-keep-operator.md)
  * 使用 [摘要運算子](../query/summarizeoperator.md) 來取得匯總資料。
* 暫時增加該查詢的相關查詢限制。 如需詳細資訊，請參閱 [查詢限制-每個反覆運算器的記憶體限制](querylimits.md)。 但是，不建議使用這個方法。 有個限制可保護叢集，並確保單一查詢不會中斷在叢集上執行的並行查詢。
