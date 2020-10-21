---
title: 'cursor_after ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 cursor_after。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 3b1a89ab84b62241058a24573c0362f7215f82c7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252475"
---
# <a name="cursor_after"></a>cursor_after()

::: zone pivot="azuredataexplorer"

資料表記錄上的述詞，用來比較其內嵌時間與資料庫資料指標。

## <a name="syntax"></a>語法

`cursor_after``(` *RHS*`)`

## <a name="arguments"></a>引數

* *RHS*：空字串常值，或有效的資料庫資料指標值。

## <a name="returns"></a>傳回

型別的純量值 `bool` ，指出是否在資料庫資料指標 *RHS* (之後內嵌記錄， `true`) 或不 (`false`) 。

**備註**

如需資料庫資料指標的其他詳細資料，請參閱 [資料庫資料指標](../management/databasecursor.md) 。

只有在已啟用 [IngestionTime 原則](../management/ingestiontimepolicy.md) 的資料表記錄上，才能叫用此函數。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
