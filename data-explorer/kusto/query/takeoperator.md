---
title: take 運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 take 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 98586f8f8380d4c1fc36a88b288b47798c10e09e
ms.sourcegitcommit: 4eb64e72861d07cedb879e7b61a59eced74517ec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2020
ms.locfileid: "85517914"
---
# <a name="take-operator"></a>take 運算子

傳回指定數目的資料列。

```kusto
T | take 5
```

除非來源資料已排序，否則不保證會傳回哪些記錄。

**語法**

`take`*NumberOfRows* 
 NumberOfRows `limit`*NumberOfRows*

（ `take` 和 `limit` 都是同義字）。

**注意事項**

`take`當您以互動方式流覽資料時，是一種簡單快速且有效率的方式來查看記錄的小型樣本，但請注意，在執行多次時，即使資料集尚未變更，它也不保證其結果的一致性。

即使查詢所傳回的資料列數目未明確受到查詢限制（不 `take` 使用任何運算子），Kusto 預設會限制該數位。
如需詳細資訊，請參閱[Kusto 查詢限制](../concepts/querylimits.md)。

請參閱： [sort 運算子](sortoperator.md) 
 [top 運算子](topoperator.md) 
 [top-nested 運算子](topnestedoperator.md)

## <a name="does-kusto-support-paging-of-query-results"></a>Kusto 支援查詢結果的分頁嗎？

Kusto 不會提供內建的分頁機制。

Kusto 是一種複雜的服務，會持續優化它所儲存的資料，以提供優於大型資料集的絕佳查詢效能。 雖然分頁是適用于具有有限資源的無狀態用戶端的實用機制，但它會將負擔轉移到必須追蹤用戶端狀態資訊的後端服務。 接下來，後端服務的效能和擴充性受到嚴格限制。

如需分頁支援，請執行下列其中一項功能：

* 將查詢的結果匯出至外部儲存體，並透過產生的資料進行分頁。

* 藉由快取 Kusto 查詢的結果，撰寫可提供具狀態分頁 API 的中介層應用程式。
