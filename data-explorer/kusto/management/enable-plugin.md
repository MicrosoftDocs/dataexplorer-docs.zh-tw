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
ms.openlocfilehash: a44ebec6774374f4d38dfda3babe42f2f5e07ac6
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422053"
---
# <a name="enable-plugin"></a>。啟用外掛程式

啟用外掛程式。

此命令需要 `All Databases admin` 許可權。

## <a name="syntax"></a>Syntax

`.enable``plugin` *PluginName*

## <a name="example"></a>範例

<!-- csl -->
```kusto
.enable plugin autocluster
``` 

## <a name="next-steps"></a>後續步驟

* [。停用外掛程式](disable-plugin.md)
* [。顯示外掛程式](show-plugins.md)

