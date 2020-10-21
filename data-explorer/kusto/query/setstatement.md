---
title: Set 語句-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 Set 語句。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b8b30aafd8fafc2a900fe0596243a0d4ed89f276
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252067"
---
# <a name="set-statement"></a>Set 陳述式

::: zone pivot="azuredataexplorer"

`set`語句是用來設定查詢持續時間的查詢選項。
查詢選項可控制查詢如何執行和傳回結果。 它們可以是預設 (關閉的布林值旗標) 或具有整數值。 查詢可能包含零個、一個或多個 set 陳述式。 Set 語句只會影響以程式順序記錄的表格式運算式語句。

* 您也可以藉由在物件中設定查詢選項，以程式設計方式加以啟用 `ClientRequestProperties` 。 請參閱 [這裡](../api/netfx/request-properties.md)。
  
* 查詢選項不是 Kusto 語言的正式部分，而且可能會在不被視為中斷語言變更的情況下修改。

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
