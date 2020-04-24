---
title: 串流式處理策略 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的流式處理策略。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2020
ms.openlocfilehash: ef4199e0cd8c1e14839e5508a9c6a0d793de0cc0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519596"
---
# <a name="streaming-ingestion-policy"></a>串流內嵌原則

## <a name="streaming-ingestion-target-scenario"></a>串流式引入目標方案

對於需要低延遲且引入時間小於 10 秒的各種卷數據的方案,流式處理面向目標。 它用於優化許多表的操作處理,在一個或多個資料庫中,其中數據流到每個表相對較小(每秒記錄很少),但總體數據引入量很高(每秒數千條記錄)。

當數據量增長到每表超過每秒 1 MB 時,請使用經典(批量)引入,而不是流式處理。 

* 要瞭解如何實現此功能,請參閱[串流引入](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming)。
* 有關流引入控制命令的資訊,請參閱[控制項指令用於管理流式引入策略](../management/streamingingestion-policy.md)

## <a name="streaming-ingestion-policy-definition"></a>串流式引入策略定義

可以在表或資料庫上定義流引入策略。 在資料庫級別定義此策略將相同的設置應用於資料庫中的所有現有和未來表。 如果在表和資料庫級別設置流式處理策略,則表級別設置優先。

> [!NOTE]
> 如果表沒有直接獲取流式處理,但只能通過更新策略進行,則不必在此表上定義流引入策略。 

流引入策略設置將分發表流數據的最大行存儲數。 可用性和數據速率支援都需要分發。

## <a name="setting-the-number-of-row-stores"></a>設定儲存記憶體

需要定義流引入策略中設置的行存儲數。 此數位應基於每個表的流數據速率(粗略估計就足夠了)。
任何表建議的最小行存儲數為 4。 支援的行存儲的最大數量為 64。
表的流數據速率越高,關聯的流引入策略中所需的行存儲數量就越高。
對推薦的設置使用下表(如果有疑問,請使用較高的數位):

|估計峰值每小時流資料速率(每表)|儲存記憶體|
|----------|------|
|< 1 Gb/小時 |4|
|1 - 2 GB/小時 |4-8|
|2 - 3 GB/小時 |8-12|
|3 - 4 GB/小時 |12-16|
| > 4 GB/小時 |

 有關此的進一步建議,請打開[支援票證](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

為獲得最佳查詢延遲,每個表定義的行存儲數不應大大超過上述建議。

> [!NOTE]
> 為資料庫設置流式處理策略時,分配數據速率最高的表所需的行存儲數。 