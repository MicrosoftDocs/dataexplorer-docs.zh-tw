---
title: 查詢結果集已超過內部...限制 - Azure 資料資源管理員 |微軟文件
description: 本文介紹查詢結果集已超過內部...Azure 資料資源管理器中的限制。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: efb07e68922a88e2cf59ef1eefeabd3af085aed4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523013"
---
# <a name="query-result-set-has-exceeded-the-internal--limit"></a>查詢結果集已超過內部...限制

*查詢結果集已超過內部...限制*是一種[部分查詢失敗](partialqueryfailures.md),當查詢結果超過兩個限制之一時發生:
* 記錄數的限制(,`record count limit`預設情況下設定為 500,000)。
* 對資料總量的限制(,`data size limit`預設情況下設定為 67,108,864 (64MB)。 

發生這種情況時,可能採取以下幾種操作方案:
* 更改查詢以消耗更少的資源。 例如,可以嘗試限制查詢返回的記錄數(使用[take 運算符](../query/takeoperator.md)或添加其他[where 子句](../query/whereoperator.md)),或減少查詢返回的列數(使用[專案運算符](../query/projectoperator.md)或[專案離開運算符](../query/projectawayoperator.md)),或使用[匯總運算符](../query/summarizeoperator.md)獲取聚合數據等。
* 暫時增加該查詢的相關查詢限制(請參閱[查詢限制](querylimits.md)下**的結果截斷**)。  
  請注意,通常不建議這樣做,因為存在限制,專門保護群集,以確保單個查詢不會中斷群集上運行的併發查詢。