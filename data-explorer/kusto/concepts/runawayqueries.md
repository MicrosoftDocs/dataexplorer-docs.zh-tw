---
title: 失控查詢 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 Runaway 查詢。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 076afea64cf3c4405f37e9e4014584b90d58ca07
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522979"
---
# <a name="runaway-queries"></a>失控查詢

*失控查詢*是在查詢執行期間超出某些內部[查詢限制](querylimits.md)時發生的一種[部分查詢失敗](partialqueryfailures.md)。

例如,可以回報以下錯誤:`HashJoin operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete.`

發生這種情況時,可能採取以下幾種操作方案:
* 更改查詢以消耗更少的資源。 例如,如果錯誤指示查詢結果集太大,則可以嘗試限制查詢返回的記錄數(使用[take 運算符](../query/takeoperator.md)或通過添加其他[位置子句](../query/whereoperator.md)),或減少查詢返回的列數(使用[專案運算符](../query/projectoperator.md)或[專案離開運算符](../query/projectawayoperator.md)),或使用[匯總運算符](../query/summarizeoperator.md)獲取聚合數據等。
* 暫時增加該查詢的相關查詢限制(請參閱[查詢限制](querylimits.md)下**每個結果集反覆運算器的 Max 記憶體**)。  
  請注意,通常不建議這樣做,因為存在限制,專門保護群集,以確保單個查詢不會中斷群集上運行的併發查詢。
  
  
  