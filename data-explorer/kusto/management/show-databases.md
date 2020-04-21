---
title: .顯示資料庫 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 .顯示 Azure 數據資源管理器中的資料庫。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1827c3ea20d984c6846ef9feb603398020efe275
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610188"
---
# <a name="show-databases"></a>.顯示資料庫

返回一個表,其中每個記錄對應於使用者有權訪問的群集中的資料庫。

要檢視傳回顯示內容的表,請參考[.show 資料庫](show-database.md)。

**語法**

`.show` `databases`

**輸出結構描述**

|資料行名稱       |資料行類型|描述                                                                  |
|------------------|-----------|-----------------------------------------------------------------------------|
|DatabaseName      |`string`   |附加到群集的資料庫的名稱。                         |
|持久存儲 |`string`   |資料庫的持久存儲"根"。 (僅供內部使用。)      |
|版本           |`string`   |資料庫版本。 (僅供內部使用。)                        |
|IsCurrent         |`bool`     |此資料庫是否是請求的資料庫上下文。                |
|資料庫存取模式|`string`   |`ReadWrite`的一個`ReadOnly``ReadOnlyFollowing`,`ReadWriteEphemeral`或 。|
|漂亮名稱        |`string`   |資料庫的漂亮名稱(如果有)。                                    |
|預留插槽1     |`bool`     |已保留。 (僅供內部使用。)                                           |
|DatabaseId        |`guid`     |資料庫的全域唯一標識符。 (僅供內部使用。)      |
