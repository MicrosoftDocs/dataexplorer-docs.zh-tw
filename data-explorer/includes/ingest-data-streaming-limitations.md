---
title: 包含檔案
description: 包含檔案
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 07/13/2020
ms.author: orspodek
ms.reviewer: alexefro
ms.custom: include file
ms.openlocfilehash: 60831cd9e4af4890a4afe788a42cbbd5cbb641aa
ms.sourcegitcommit: 35236fefb52978ce9a09bc36affd5321acb039a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97531833"
---
## <a name="limitations"></a>限制

* 如果資料庫本身或其任何資料表已定義並啟用[串流內嵌原則](../kusto/management/streamingingestionpolicy.md)，資料庫不支援資料庫[資料指標](../kusto/management/databasecursor.md)。
* 必須[預先建立](../kusto/management/create-ingestion-mapping-command.md)[資料](../kusto/management/mappings.md)對應，才能在串流內嵌中使用。 個別的串流內嵌要求無法容納內嵌資料對應。
* 串流內嵌效能和容量可透過增加的 VM 和叢集大小進行調整。 並行內嵌要求的數目限制為每個核心六個。 例如，針對16個核心 Sku （例如 D14 和 L16 也），最大支援的負載為96個並行內嵌要求。 針對兩個核心 Sku （例如 D11），最大支援的負載是12個並行內嵌要求。
* 串流內嵌要求的資料大小限制為 4 MB。
* 架構更新（例如建立和修改資料表和內嵌對應）最多可能需要五分鐘的串流內嵌服務。 如需詳細資訊，請參閱 [串流內嵌和架構變更](../kusto/management/data-ingestion/streaming-ingestion-schema-changes.md)。
* 即使資料未透過串流內嵌，也會在叢集上啟用串流內嵌，並使用叢集機器的本機 SSD 磁片的一部分來串流內嵌資料，並減少可用於熱快取的儲存體。
* 無法在串流內嵌資料上設定[延伸區標記](../kusto/management/extents-overview.md#extent-tagging)。
* [更新原則](../kusto/management/updatepolicy.md)。 更新原則只能參考來源資料表中新內嵌的資料，而不是資料庫中的任何其他資料或資料表。
* 如果在資料庫的任何資料表上使用串流內嵌，則此資料庫不能當做資料提供者 [資料庫](../follower.md) 的領導者，或做為 Azure Data Share 的 [資料提供者](../data-share.md#data-provider---share-data) 使用。
