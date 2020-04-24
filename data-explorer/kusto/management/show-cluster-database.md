---
title: .顯示群組資料庫 - Azure 資料資源管理員 |微軟文件
description: 本文介紹.顯示 Azure 數據資源管理器中的群集資料庫。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f354862df1bc9bef352819832125cf6f82ba0ae4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519987"
---
# <a name="show-cluster-databases"></a>.顯示群組資料庫

返回一個表,顯示附加到群集的所有資料庫,以及調用命令的使用者有權訪問這些資料庫。 如果使用特定的資料庫名稱,則僅包含這些資料庫。

**語法**

`.show` `cluster` `databases` [`details` | `identity` | `policies` | `datastats`]

`.show``cluster``databases``(`資料庫1`,`資料庫 2 ... `,`資料庫N`)`

**輸出**
 
|輸出參數 |類型 |描述 
|---|---|---
|DatabaseName  |String |資料庫名稱。 資料庫名稱區分大小寫。 
|持久存儲  |String |存儲資料庫的持久存儲URI。 (此欄位對於臨時資料庫為空。 
|版本  |String |資料庫版本號碼。 對於資料庫中的每個更改操作(如添加數據和更改架構),此數位將更新。 
|IsCurrent  |Boolean |如果資料庫是當前連接指向的,則為 True。 
|資料庫存取模式  |String |群集如何附加到資料庫。 例如,如果資料庫以 ReadOnly 模式連接,群集將失敗所有以任何方式修改資料庫的請求。 
|漂亮名稱 |String |資料庫名稱相當。
|目前使用者沒有限制檢視器 |Boolean | 指定當前使用者是否為資料庫上的無限制查看器。
|DatabaseId |Guid |資料庫的唯一 ID。