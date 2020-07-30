---
title: cursor_after （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 cursor_after （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 0f555cd1ebec8d95a3e7d0e46c986b04154c721e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348627"
---
# <a name="cursor_after"></a>cursor_after()

::: zone pivot="azuredataexplorer"

資料表記錄上的述詞，用來比較其對資料庫資料指標的內嵌時間。

## <a name="syntax"></a>語法

`cursor_after``(` *RHS*`)`

## <a name="arguments"></a>引數

* *RHS*：空字串常值，或有效的資料庫資料指標值。

## <a name="returns"></a>傳回

類型的純量值 `bool` ，表示在資料庫資料指標*RHS* （ `true` ）或不是（）之後是否內嵌記錄 `false` 。

**備註**

如需資料庫資料指標的其他詳細資料，請參閱[資料庫資料指標](../management/databasecursor.md)。

這個函數只能在已啟用[IngestionTime 原則](../management/ingestiontimepolicy.md)的資料表記錄上叫用。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
