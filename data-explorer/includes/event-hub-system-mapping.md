---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 12/22/2020
ms.author: orspodek
ms.openlocfilehash: 52a6de4c8ecb3ccbe4e315145b8ae8a3c314ebfc
ms.sourcegitcommit: 600c3430c00eb62d52540fe08dab386a860193cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2021
ms.locfileid: "102472193"
---
> [!NOTE]
> * 系統屬性支援 `json` 和表格式格式 (等 `csv` ) ， `tsv` 壓縮的資料則不支援。 使用不支援的格式時，資料仍會內嵌，但屬性將會被忽略。
> * 針對表格式資料，只支援單一記錄事件訊息的系統屬性。
> * 針對 JSON 資料，多記錄事件訊息也支援系統屬性。 在這種情況下，系統屬性只會新增至事件訊息的第一筆記錄。 
> * 針對 `csv` 對應，屬性會依 [ [系統屬性](../ingest-data-event-hub-overview.md#system-properties) ] 資料表中所列的順序加入至記錄的開頭。
> * 針對對應 `json` ，會根據 [ [系統屬性](../ingest-data-event-hub-overview.md#system-properties) ] 資料表中的屬性名稱來新增屬性。
