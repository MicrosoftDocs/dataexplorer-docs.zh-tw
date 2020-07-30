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
ms.openlocfilehash: 879fe4aac2a6714f3d7ab16a63c69cd8215bbb04
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348593"
---
# <a name="cursor_current-current_cursor"></a>cursor_current()、current_cursor()

::: zone pivot="azuredataexplorer"

在範圍內，抓取資料庫之資料指標的目前值。 （名稱 `cursor_current` 和 `current_cursor` 都是同義字）。

## <a name="syntax"></a>語法

`cursor_current()`

## <a name="returns"></a>傳回

傳回類型的單一值 `string` ，其會將範圍中資料庫之資料指標的目前值編碼。

**備註**

如需資料庫資料指標的其他詳細資料，請參閱[資料庫資料指標](../management/databasecursor.md)。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
