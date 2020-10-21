---
title: take 運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 take 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5192bb2d752a5754ae36840611b9f7b0e84da256
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250730"
---
# <a name="take-operator"></a>take 運算子

回到指定的資料列數目。

```kusto
T | take 5
```

除非來源資料已排序，否則不保證會傳回哪些記錄。

> [!NOTE]
> `take` 當您以互動方式流覽資料時，這是一種簡單、快速且有效率的方式來查看記錄的小型樣本，但請注意，在執行多次時，不會保證其結果中的任何一致性，即使資料集未變更也是一樣。
> 即使查詢所傳回的資料列數目未明確受查詢限制 (`take` 也不會使用任何運算子) ，Kusto 預設會限制該數目。 如需詳細資訊，請參閱 [Kusto 查詢限制](../concepts/querylimits.md)。

## <a name="syntax"></a>語法

`take`*NumberOfRows* 
 NumberOfRows `limit`*NumberOfRows*

 (`take` 和 `limit` 都是同義字。 ) 

## <a name="does-kusto-support-paging-of-query-results"></a>Kusto 是否支援查詢結果的分頁？

Kusto 不會提供內建的分頁機制。

Kusto 是一項複雜的服務，可持續將它所儲存的資料優化，以提供優於大量資料集的絕佳查詢效能。 雖然分頁對於具有有限資源的無狀態用戶端來說很有用，但它會將負擔轉移到需要追蹤用戶端狀態資訊的後端服務。 之後端服務的效能和擴充性會受到嚴格限制。

針對分頁支援，請執行下列其中一項功能：

* 將查詢結果匯出至外部儲存體，並透過產生的資料進行分頁。

* 藉由快取 Kusto 查詢的結果，撰寫可提供具狀態頁面 API 的仲介層應用程式。

## <a name="see-also"></a>另請參閱

* [sort 運算子](sortoperator.md)
* [top 運算子](topoperator.md)
* [top-nested 運算子](topnestedoperator.md)