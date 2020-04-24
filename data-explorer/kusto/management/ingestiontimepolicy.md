---
title: 引入時間策略 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的引入時間策略。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 0c1755115a9e9f4c60c7f5574eb9b784520ecb88
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520820"
---
# <a name="ingestiontime-policy"></a>IngestionTime 原則

引入時間策略是可在表上設置(啟用)的可選策略。

在表上啟用此政策時,Kusto 會向`datetime`表 新增隱藏列,`$IngestionTime`稱為 。 從那時起,每當將新數據引入到表中時,攝入的時間(如提交數據之前 Kusto 群集測量)都會記錄在隱藏列中,用於攝入的所有記錄。 (請注意,每個記錄都有自己的`$IngestionTime`值,就像任何其他列一樣。

由於引入時間列是隱藏的,因此不能直接查詢其值。
相反,將提供一個名為[ingestion_time()的特殊](../query/ingestiontimefunction.md)函數來檢索該值。 如果表中沒有此類列,或者在引入記錄時未啟用 IngingTime 策略,則返回 null 值。

引入時間策略專為兩種主要方案而設計:
* 允許用戶估計引入數據的端到端延遲。
  許多包含日誌數據的表都有一些時間戳列,其值由源填充,以指示生成記錄的時間。 通過將該列的值與引入時間列進行比較,可以估計獲取數據的延遲。 (請注意,這隻是一個估計值,因為源和 Kusto 不一定同步其時鐘。
* 支持[資料庫游標](../management/databasecursor.md),允許使用者發出連續查詢,並且每次將查詢限制為自上次查詢以來已引入的數據。



有關管理引入時間政策的控制命令,[請參考此處](../management/ingestiontime-policy.md)。