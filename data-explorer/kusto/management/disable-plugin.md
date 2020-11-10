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
ms.openlocfilehash: 1dd2649d4746506f0ef2c08d4615260babd594e1
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422056"
---
# <a name="disable-plugin"></a>。停用外掛程式

停用外掛程式。

此命令需要 `All Databases admin` 許可權。

## <a name="syntax"></a>Syntax

`.disable``plugin` *PluginName*

## <a name="example"></a>範例
 
<!-- csl -->
```kusto
.disable plugin autocluster
``` 

## <a name="next-steps"></a>後續步驟

* [。顯示外掛程式](show-plugins.md)
* [。啟用外掛程式](enable-plugin.md)

