---
title: 外掛程式命令顯示外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的外掛程式管理命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/02/2020
ms.openlocfilehash: 1db761d8c290c7aaea452cd0cb3d85f2f648221c
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422057"
---
# <a name="show-plugins"></a>。顯示外掛程式


列出叢集中的所有外掛程式。

## <a name="syntax"></a>Syntax

`.show` `plugins`

## <a name="output"></a>輸出

傳回包含下欄欄位的資料表：
* **PluginName** ：外掛程式的名稱
* **IsEnabled** ：布林值，指出是否已啟用外掛程式

## <a name="example"></a>範例

<!-- csl -->
```kusto
.show plugins
``` 

| PluginName | IsEnabled |
|---|---|
| autocluster | false |
| basket      | true  |

## <a name="next-steps"></a>後續步驟

* [。停用外掛程式](disable-plugin.md)
* [。啟用外掛程式](enable-plugin.md)
