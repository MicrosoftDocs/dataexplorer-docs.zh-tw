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
ms.openlocfilehash: 312e6cad984491b39bd9a77f0b34d142798e922e
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257989"
---
# <a name="streaming-ingestion-policy"></a>串流內嵌原則

## <a name="streaming-ingestion-target-scenario"></a>串流內嵌目標案例

串流內嵌的目標是需要低延遲的案例，針對各種磁片區資料的內嵌時間小於10秒。 它是用來在一或多個資料庫中優化許多資料表的工作處理，其中每個資料表中的資料流程相對較小（每秒幾筆記錄），但整體資料內嵌磁片區很高（每秒數千筆記錄）。

當資料量成長到每個資料表每小時 4 Gb 以上時，請使用傳統（大量）內嵌，而不是串流內嵌。 

* 若要瞭解如何執行這項功能，請參閱[串流](../../ingest-data-streaming.md)內嵌。
* 如需串流內嵌控制命令的相關資訊，請參閱[控制用於管理串流內嵌原則的命令](streamingingestion-policy.md)
 
## <a name="streaming-ingestion-policy-definition"></a>串流內嵌原則定義

串流內嵌原則包含下列屬性：

* **IsEnabled**：
  * 定義資料表/資料庫的串流內嵌功能狀態
  * 必要，沒有預設值，必須明確地設定為*true*或*false*
* **HintAllocatedRate**：
  * 如果 set 在資料表所預期的每小時資料量（gb）中提供提示。 此提示可協助系統調整針對資料表所配置的資源量，以支援串流內嵌。
  * 預設值*為 null* （未設定）

若要在資料表上啟用串流內嵌，請定義串流內嵌原則，並將*IsEnabled*設為*true*。 這個定義可以在資料表本身或資料庫上設定。
在資料庫層級定義此原則，會將相同的設定套用到資料庫中所有現有和未來的資料表。 如果同時在資料表和資料庫層級設定串流內嵌原則，則會優先使用資料表層級設定。 此設定表示可以針對資料庫一般啟用串流內嵌，但特別針對特定資料表停用，或另一種方式。

> [!NOTE]
> 如果資料表未直接取得串流內嵌，但只有透過更新原則，則不需要在此資料表上定義串流內嵌原則。


## <a name="set-the-data-rate-hint"></a>設定資料速率提示
串流內嵌原則可以針對資料表預期的每小時資料量提供提示。 此提示會協助系統調整為此資料表配置的資源數量，以支援串流內嵌。
如果將串流資料輸入至資料表的速率會超過 1 Gb/小時，請設定提示。
如果在資料庫的串流內嵌原則中設定_HintAllocatedRate_ ，請將它設定為具有最高預期資料速率的資料表。 不建議將資料表的有效提示設定為比預期的每小時尖峰資料速率高得多的值。 此設定可能會對查詢效能造成負面影響。
