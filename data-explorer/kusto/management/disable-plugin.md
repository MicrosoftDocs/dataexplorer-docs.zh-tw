---
title: 停用外掛程式命令-Azure 資料總管
description: 本文說明外掛程式管理命令。請在 Azure 資料總管中停用外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/02/2020
ms.openlocfilehash: 1063355f880f26a321eb6416180ae9764575f0be
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320888"
---
# <a name="disable-plugin"></a>.disable 外掛程式

停用外掛程式。

此命令需要 `All Databases admin` 許可權。

## <a name="syntax"></a>語法

`.disable``plugin` *PluginName*

## <a name="example"></a>範例
 
<!-- csl -->
```kusto
.disable plugin autocluster
``` 

## <a name="next-steps"></a>後續步驟

* [`.show plugins`](show-plugins.md)
* [`.enable plugin`](enable-plugin.md)

