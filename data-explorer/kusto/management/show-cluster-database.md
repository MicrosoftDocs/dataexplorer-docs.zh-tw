---
title: 。顯示叢集資料庫-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中顯示叢集資料庫。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c6d25380a44a2195f407c52a2224ee28fad9f8bb
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320497"
---
# <a name="show-cluster-databases"></a>.show cluster databases

傳回資料表，其中顯示附加至叢集的所有資料庫，以及叫用命令的使用者具有存取權的所有資料庫。 如果使用特定的資料庫名稱，則只會包含這些資料庫。

**語法**

`.show` `cluster` `databases` [`details` | `identity` | `policies` | `datastats`]

`.show``cluster` `databases` `(`database1 `,` database2 `,` .。。databaseN`)`

**輸出**
 
|輸出參數 |類型 |描述 
|---|---|---
|DatabaseName  |String |資料庫名稱。 資料庫名稱會區分大小寫。 
|PersistentStorage  |String |儲存資料庫的持續性儲存體 URI。  (暫時資料庫的這個欄位是空的。 )  
|版本  |String |資料庫版本號碼。 資料庫 (中的每個變更作業都會更新這個數位，例如新增資料和變更架構) 。 
|IsCurrent  |Boolean |如果資料庫是目前連接所指向的資料庫，則為 True。 
|DatabaseAccessMode  |String |叢集如何連接至資料庫。 例如，如果資料庫是以唯讀模式附加，叢集將會失敗所有以任何方式修改資料庫的要求。 
|PrettyName |String |資料庫的名稱。
|CurrentUserIsUnrestrictedViewer |Boolean | 指定目前的使用者是否為資料庫上不受限制的檢視器。
|DatabaseId |Guid |資料庫的唯一識別碼。