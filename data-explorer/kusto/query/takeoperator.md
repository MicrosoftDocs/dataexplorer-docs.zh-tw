---
title: 取得運算子 - Azure 資料資源管理員 |微軟文件
description: 本文介紹了 Azure 數據資源管理器中的獲取運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0c8f724e139e13bf9ece00d5af09f2a3cf25b03a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506591"
---
# <a name="take-operator"></a>take 運算子

返回到指定的行數。

```kusto
T | take 5
```

除非對源數據進行排序,否則無法保證返回哪些記錄。

**語法**

`take`*NumberOfRows*`limit`*行數的行數*


(`take``limit`是 同義詞。

**注意事項**

`take`在互動式流覽數據時查看少量記錄示例是一種簡單、快速、高效的方法,但請注意,即使數據集未更改,在執行多次時,它不能保證結果的任何一致性。

即使查詢返回的行數不受查詢的明確限制(不使用`take`運算符),Kusto 默認也會限制該數位。
有關詳細資訊,請參閱[Kusto 查詢限制](../concepts/querylimits.md)。

請參閱:[對運算元](sortoperator.md)
[頂部運算元](topoperator.md)
[頂部嵌so運算符進行排序](topnestedoperator.md)

## <a name="does-kusto-support-paging-of-query-results"></a>Kusto 是否支援查詢結果的分頁?

Kusto 不提供內置的分頁機制。

Kusto 是一項複雜的服務,它不斷優化其存儲的數據,從而在大型數據集上提供出色的查詢性能。 雖然分頁對於資源有限的無狀態客戶端來說是一種有用的機制,但它將負擔轉移到必須跟蹤用戶端狀態資訊的後端服務上。 隨後,後端服務的性能和可擴充性受到嚴重限制。

對分頁支援實現以下功能之一:

* 將查詢結果匯出到外部存儲並通過生成的數據分頁。

* 編寫一個中間層應用程式,通過緩存 Kusto 查詢的結果來提供有狀態的分頁 API。