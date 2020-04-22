---
title: 設定語句 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理員中的 Set 語句。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 8cb9c1d72f1b2e8e4bfbbd28d67c04295c9ccf5b
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765582"
---
# <a name="set-statement"></a>Set 陳述式

::: zone pivot="azuredataexplorer"

該`set`語句用於在查詢期間設置查詢選項。
查詢選項可控制查詢如何執行和傳回結果。 它們可以是布爾標誌(預設情況下關閉),也可以有一個整數值。 查詢可能包含零個、一個或多個 set 陳述式。 設定語句僅影響按程式順序跟蹤它們的表格表達式語句。

* 通過在`ClientRequestProperties`物件中設置查詢選項,也可以以程式設計方式啟用它們。 [見此處](../api/netfx/request-properties.md)。
  
* 查詢選項正式不是 Kusto 語言的一部分,可以修改而不被視為斷語更改。

**語法**

`set`*選項名稱*`=`=*選項值*:

**範例**

```kusto
set querytrace;
Events | take 100
```

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援此功能

::: zone-end
