---
title: cursor_after() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 azure 數據資源管理器中的 cursor_after()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 39ec32322b74b55182522e4bbb04aa0c3830d8d2
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766018"
---
# <a name="cursor_after"></a>cursor_after()

::: zone pivot="azuredataexplorer"

用於比較表的記錄與資料庫游標的引入時間。

**語法**

`cursor_after``(` *RHS*`)`

**引數**

* *RHS*:空字串文字或有效的資料庫游標值。

**傳回**

類型`bool`標量值,指示紀錄是在資料庫游標*RHS* (`true`)`false`之後引入的 。

**注意事項**

有關[資料庫游標的其他詳細資訊,請參考資料庫游標](../management/databasecursor.md)。

此函數只能在啟用[引入時間策略](../management/ingestiontimepolicy.md)的表的記錄上調用。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援此功能

::: zone-end
