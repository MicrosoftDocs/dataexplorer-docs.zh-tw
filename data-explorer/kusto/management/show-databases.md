---
title: 。顯示資料庫-Azure 資料總管
description: 本文說明。在 Azure 資料總管中顯示資料庫。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e5ff64db05bb85d88ed3da3347ea95cf02b0234b
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818535"
---
# <a name="show-databases"></a>.show 資料庫

傳回資料表，其中的每個記錄都會對應至使用者可以存取之叢集中的資料庫。

如需顯示內容資料庫屬性的資料表，請參閱 [`.show database`](show-database.md) 。

**語法**

`.show` `databases`

**輸出結構描述**

|資料行名稱       |資料行類型|描述                                                                  |
|------------------|-----------|-----------------------------------------------------------------------------|
|DatabaseName      |`string`   |附加至叢集的資料庫名稱                    |
|PersistentStorage |`string`   |資料庫的持續性儲存體「根」。 僅供內部使用          |
|版本           |`string`   |資料庫版本。 僅供內部使用                       |
|IsCurrent         |`bool`     |此資料庫是否為要求的資料庫內容                    |
|DatabaseAccessMode|`string`   |、、 `ReadWrite` `ReadOnly` `ReadOnlyFollowing` 或其中一個`ReadWriteEphemeral`    |
|PrettyName        |`string`   |資料庫的非常名稱（如果有的話）                        |
|ReservedSlot1     |`bool`     |已保留。 僅供內部使用              |
|DatabaseId        |`guid`     |資料庫的全域唯一識別碼。 僅供內部使用          |
