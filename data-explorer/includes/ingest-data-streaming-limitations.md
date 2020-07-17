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
ms.openlocfilehash: 33168f001dd3d998fbf82169c7637e2eb390e0bc
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423229"
---
## <a name="limitations"></a>限制

* 如果資料庫本身或其任何資料表已定義並啟用[串流內嵌原則](../kusto/management/streamingingestionpolicy.md)，資料庫就不支援資料庫[資料指標](../kusto/management/databasecursor.md)。
* [資料](../kusto/management/mappings.md)對應必須[預先建立](../kusto/management/create-ingestion-mapping-command.md)，才能用於串流內嵌。 個別的串流內嵌要求不會容納內嵌資料對應。
* 透過增加的 VM 和叢集大小，串流處理內嵌的效能和容量規模。 並行的內嵌要求數目限制為每個核心六個。 例如，針對16核心 Sku （例如 D14 和 L16 也），支援的最大負載為96並行內嵌要求。 針對兩個核心 Sku （例如 D11），支援的最大負載為12個並行內嵌要求。
* 串流內嵌要求的資料大小限制為 4 MB。
* 架構更新（例如建立和修改資料表和內嵌對應）最多可能需要五分鐘的時間來處理串流內嵌服務。 如需詳細資訊[，請參閱串流內嵌和架構變更](../kusto/management/data-ingestion/streaming-ingestion-schema-changes.md)。
* 在叢集上啟用串流內嵌，即使資料不是透過串流內嵌，也會使用叢集機器的部分本機 SSD 磁片來串流內嵌資料，並減少用於熱快取的儲存體。
* 無法在串流內嵌資料上設定[範圍標記](../kusto/management/extents-overview.md#extent-tagging)。
* 如果在資料庫的任何資料表上使用串流內嵌，則此資料庫不能當做後向[資料庫](../follower.md)的領導者，或當做 Azure 資料共用的[資料提供者](../data-share.md#data-provider---share-data)使用。
