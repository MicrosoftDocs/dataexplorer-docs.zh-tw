---
title: parse_command_line （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 parse_command_line （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: slneimer
ms.service: data-explorer
ms.topic: reference
ms.date: 06/28/2020
ms.openlocfilehash: 0296b41dc10092f0b274491c3fab3355fc82a2d9
ms.sourcegitcommit: 4eb64e72861d07cedb879e7b61a59eced74517ec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2020
ms.locfileid: "85518129"
---
# <a name="parse_command_line"></a>parse_command_line （）

剖析 Unicode 命令列字串，並傳回命令列引數的動態陣列。

**語法**

`parse_command_line(`*command_line*，*parser_type*`)`

**引數**

* *command_line*：要剖析的命令列。
* *parser_type*：目前唯一支援的值是 `"Windows"` ，它會以與[CommandLineToArgvW](https://docs.microsoft.com/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw)相同的方式剖析命令列。

**傳回**

命令列引數的動態陣列。

**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print parse_command_line("echo \"hello world!\"", "windows")
```

|結果|
|---|
|["echo"，"hello world！"]|
