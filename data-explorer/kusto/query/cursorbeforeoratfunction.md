---
title: cursor_before_or_at （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 cursor_before_or_at （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 94644ae8bfae934bc908c7e06903be3825d8ccd8
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348610"
---
# <a name="cursor_before_or_at"></a>cursor_before_or_at()

::: zone pivot="azuredataexplorer"

資料表記錄上的述詞，用來比較其對資料庫資料指標的內嵌時間。

## <a name="syntax"></a>語法

`cursor_before_or_at``(` *RHS*`)`

## <a name="arguments"></a>引數

* *RHS*：空字串常值，或有效的資料庫資料指標值。

## <a name="returns"></a>傳回

類型的純量值 `bool` ，指出記錄是在資料庫資料指標*RHS* （）之前或之後內嵌 `true` （ `false` ）。

**備註**

如需資料庫資料指標的其他詳細資料，請參閱[資料庫資料指標](../management/databasecursor.md)。

這個函數只能在已啟用[IngestionTime 原則](../management/ingestiontimepolicy.md)的資料表記錄上叫用。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
