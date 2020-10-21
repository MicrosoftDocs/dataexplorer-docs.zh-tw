---
title: 'cursor_current ( # A1、current_cursor ( # A3-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 中 cursor_current ( # A1、current_cursor ( # A3 資料總管。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 1b2e6f868d5117cbdd3db6ba88c6b77e5f0e8ccf
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252448"
---
# <a name="cursor_current-current_cursor"></a>cursor_current()、current_cursor()

::: zone pivot="azuredataexplorer"

抓取範圍中資料庫之資料指標的目前值。  (名稱 `cursor_current` ，並 `current_cursor` 為同義字。 ) 

## <a name="syntax"></a>語法

`cursor_current()`

## <a name="returns"></a>傳回

傳回類型的單一值 `string` ，以編碼範圍中資料庫之資料指標的目前值。

**備註**

如需資料庫資料指標的其他詳細資料，請參閱 [資料庫資料指標](../management/databasecursor.md) 。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
