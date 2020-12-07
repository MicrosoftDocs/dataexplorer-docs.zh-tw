---
title: 啟用外掛程式命令-Azure 資料總管
description: 本文說明外掛程式管理命令。請在 Azure 資料總管中啟用外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/02/2020
ms.openlocfilehash: a4da3848fa459cf5fae8a7a73f8b1f318ce7e858
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321466"
---
# <a name="enable-plugin"></a>.enable 外掛程式

啟用外掛程式。

此命令需要 `All Databases admin` 許可權。

## <a name="syntax"></a>語法

`.enable``plugin` *PluginName*

## <a name="example"></a>範例

<!-- csl -->
```kusto
.enable plugin autocluster
``` 

## <a name="next-steps"></a>後續步驟

* [`.disable plugin`](disable-plugin.md)
* [`.show plugins`](show-plugins.md)

