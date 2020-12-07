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
ms.openlocfilehash: 569554379ffe672ed75fd15da127f03fea35f6d1
ms.sourcegitcommit: 2804e3fe40f6cf8e65811b00b7eb6a4f59c88a99
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96748377"
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

## <a name="paging-of-query-results"></a>查詢結果的分頁

執行分頁的方法包括：

* 將查詢結果匯出至外部儲存體，並透過產生的資料進行分頁。
* 撰寫中介層應用程式，藉由快取 Kusto 查詢的結果，提供具狀態的頁面 API。
* 在 [預存查詢結果](../management/stored-query-results.md#pagination) 中使用分頁。


## <a name="see-also"></a>另請參閱

* [sort 運算子](sortoperator.md)
* [top 運算子](topoperator.md)
* [top-nested 運算子](topnestedoperator.md)
