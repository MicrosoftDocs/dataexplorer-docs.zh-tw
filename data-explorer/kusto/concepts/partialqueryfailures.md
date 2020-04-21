---
title: 部份查詢失敗 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的部分查詢失敗。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d65256c0ac50a80e3e3b095e8d2348dbae5e585b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523115"
---
# <a name="partial-query-failures"></a>部分查詢失敗

*部分查詢失敗*是運行僅在查詢開始實際執行階段後檢測到的查詢失敗。 此時,Kusto 已經將 HTTP`200 OK`狀態行 返回用戶端,並且無法"將其收回",因此必須指示將查詢結果傳回用戶端的結果流中的失敗。 (事實上,它可能已經將一些結果數據返回給調用方。

有幾種類型的部份查詢失敗:
* [失控查詢](runawayqueries.md):佔用過多資源的查詢。
* [結果截斷](resulttruncation.md):其結果集因超出一定限制而截斷的查詢。
* [溢出](overflow.md):觸發溢出錯誤的查詢。

可以通過以下兩種方式之一向客戶端報告部分查詢失敗:

* 作為結果數據的一部分(一種特殊的記錄表示這不是數據本身)。 這是預設方式。
* 作為結果流中的"查詢狀態"表的一部分。 這是通過使用請求槽中的`deferpartialqueryfailures`選項`properties``Kusto.Data.Common.ClientRequestProperties.OptionDeferPartialQueryFailures`() 來完成的。
  執行該操作的客戶端負責從服務消耗整個結果流、查`QueryStatus`找 結果並確保此結果中沒有記錄`Severity``2`具有 或 更小的記錄。 