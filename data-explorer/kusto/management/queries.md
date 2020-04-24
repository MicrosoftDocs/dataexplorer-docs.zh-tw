---
title: 查詢管理 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的查詢管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: b888aa004b4c8b02cd4e1d130bb0b5319af018b3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520514"
---
# <a name="queries-management"></a>查詢管理

## <a name="show-queries"></a>.顯示查詢

這個`.show``queries`指令傳回已達到最終狀態的查詢清單,並且呼叫該命令的使用者有權存取:


* [資料庫管理員或資料庫監視器](../management/access-control/role-based-authorization.md)可以看到在其資料庫上調用的任何命令。
* 其他使用者只能看到由他們調用的查詢。

**語法**

`.show` `queries`

## <a name="show-running-queries"></a>.顯示正在執行查詢

`.show`該`queries`命令返回使用者、其他使用者或所有使用者當前執行的`running`查詢的清單。

**語法**

```kusto
.show running queries
```

* (1) 由調用使用者返回當前執行的查詢(需要讀取存取)。

## <a name="cancel-query"></a>.取消查詢

該`.cancel``query`命令啟動盡最大努力嘗試取消以前由同一使用者啟動的特定查詢。

* 群集管理員可以取消任何正在運行的查詢。
* 資料庫管理員可以取消在具有管理員訪問許可權的資料庫上調用的任何正在運行的查詢。
* 其他使用者只能取消他們啟動的查詢。 

**語法**

`.cancel``query`*用戶端要求 Id*

* *用戶端請求Id*是原始查詢用戶端RequestId欄位的值,`string`作為文本。

**範例**

```kusto
.cancel query "KE.RunQuery;8f70e9ab-958f-4955-99df-d2a288b32b2c"
```

