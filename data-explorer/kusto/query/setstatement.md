---
title: Set 語句-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 Set 語句。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: fbb5d3765b4be20b55cd7e3fa155a26e429c61e8
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351143"
---
# <a name="set-statement"></a>Set 陳述式

::: zone pivot="azuredataexplorer"

`set`語句可用來設定查詢持續時間的查詢選項。
查詢選項可控制查詢如何執行和傳回結果。 它們可以是布林旗標（預設為關閉），或具有整數值。 查詢可能包含零個、一個或多個 set 陳述式。 Set 語句只會影響以程式順序追蹤的表格式運算式語句。

* 查詢選項也可以藉由在物件中設定來以程式設計方式啟用 `ClientRequestProperties` 。 請參閱[這裡](../api/netfx/request-properties.md)。
  
* 查詢選項不是 Kusto 語言的正式部分，而且可以在不被視為中斷的語言變更的情況下修改。

## <a name="syntax"></a>語法

`set`*選項名稱*[ `=` *OptionValue*]

## <a name="example"></a>範例

```kusto
set querytrace;
Events | take 100
```

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
