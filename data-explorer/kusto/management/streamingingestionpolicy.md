---
title: 串流內嵌原則-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的串流內嵌原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2020
ms.openlocfilehash: a0141482e3714ed710dbdc6fa00e3b8b396e77e6
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373299"
---
# <a name="streaming-ingestion-policy"></a>串流內嵌原則

## <a name="streaming-ingestion-target-scenario"></a>串流內嵌目標案例

串流內嵌的目標是針對不同的磁片區資料，在需要低延遲的情況下，有一段時間小於10秒的案例。 它可用來在一或多個資料庫中優化許多資料表的工作處理，其中每個資料表中的資料流程相對較小（每秒少筆記錄），但整體資料內嵌磁片區為高（每秒數千筆記錄）。

當資料量成長到每個資料表每秒 1 MB 以上時，請使用傳統（大量）內嵌，而不是串流內嵌。 

* 若要瞭解如何執行這項功能，請參閱[串流](../../ingest-data-streaming.md)內嵌。
* 如需串流內嵌控制命令的相關資訊，請參閱[控制命令可用來管理串流內嵌原則](../management/streamingingestion-policy.md)

## <a name="streaming-ingestion-policy-definition"></a>串流內嵌原則定義

串流內嵌原則可以定義于資料表或資料庫上。 在資料庫層級定義此原則，會將相同的設定套用到資料庫中所有現有和未來的資料表。 如果同時在資料表和資料庫層級設定串流內嵌原則，則會優先使用資料表層級設定。

> [!NOTE]
> 如果資料表未直接取得串流內嵌，但只有透過更新原則，則不需要在此資料表上定義串流內嵌原則。 

串流內嵌原則會設定將在其中散發資料表串流資料的資料列存放區數目上限。 可用性和資料速率支援需要散發。

## <a name="setting-the-number-of-row-stores"></a>設定資料列存放區的數目

必須定義串流內嵌原則中所設定的資料列存放區數目。 這個數位應該以每個資料表的串流資料速率為依據（粗略估計就已足夠）。
任何資料表的最小建議資料列存放區數目為四個。 支援的資料列存放區數目上限為64。
資料表的串流資料速率愈高，相關聯的串流內嵌原則所需的必要資料列存放區數目就愈高。
請使用下表來取得建議的設定（如果不確定使用較高的數位）：

|預估尖峰每小時串流資料速率（每份資料表）|資料列存放區的數目|
|----------|------|
|< 1 Gb/小時 |4|
|1-2 GB/小時 |4-8|
|2-3 GB/小時 |8-12|
|3-4 GB/小時 |12-16|
| > 4 GB/小時 |

 如需有關此情況的進一步建議，請開啟[支援票證](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

為了達到最佳查詢延遲，每個資料表所定義的資料列存放區數目不應明顯超過上述建議。

> [!NOTE]
> 設定資料庫的串流處理內嵌原則時，請針對資料速率最高的資料表，指派所需的資料列存放區數目。 