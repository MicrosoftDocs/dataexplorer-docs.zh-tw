---
title: cursor_current （）、current_cursor （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 cursor_current （）、current_cursor （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d9a9526fd846523510e8555c04ff3345d9008348
ms.sourcegitcommit: fbe298e88542c0dcea0f491bb53ac427f850f729
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82030458"
---
# <a name="cursor_current-current_cursor"></a>cursor_current()、current_cursor()

::: zone pivot="azuredataexplorer"

在範圍內，抓取資料庫之資料指標的目前值。 （名稱`cursor_current`和`current_cursor`都是同義字）。

**語法**

`cursor_current()`

**傳回**

傳回類型`string`的單一值，其會將範圍中資料庫之資料指標的目前值編碼。

**注意事項**

如需資料庫資料指標的其他詳細資料，請參閱[資料庫資料指標](../management/databasecursor.md)。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援此功能

::: zone-end
