---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 06/11/2020
ms.author: orspodek
ms.openlocfilehash: 3d366a824ecfdf89c56b1049b70df664573cd7e0
ms.sourcegitcommit: 4d5628b52b84f7564ea893f621bdf1a45113c137
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "97371644"
---
您可對資料表進行的變更視下列參數而定：
* **資料表** 類型是新的或現有的
* **對應** 類型是新的或現有的

資料表類型 | 對應類型 | 可用的調整|
|---|---|---|
|新增資料表   | 新的對應 |變更資料類型、重新命名資料行、新增資料行、刪除資料行、更新資料行、遞增排序、遞減排序  |
|現有的資料表  | 新的對應 | 新的資料行 (您可以在此變更資料類型、重新命名和更新)， <br> 更新資料行、遞增排序、遞減排序  |
| | 現有的對應 | 遞增排序、遞減排序

> [!NOTE]
> 新增新的資料行或更新資料行時，您可以變更對應轉換。 如需詳細資訊，請參閱[對應轉換](../ingest-data-one-click.md#mapping-transformations)