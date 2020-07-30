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
ms.openlocfilehash: f330c10e95cdc36eae497811ef895ef827918b43
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346485"
---
# <a name="parse_command_line"></a>parse_command_line()

剖析 Unicode 命令列字串，並傳回命令列引數的動態陣列。

## <a name="syntax"></a>語法

`parse_command_line(`*command_line*，*parser_type*`)`

## <a name="arguments"></a>引數

* *command_line*：要剖析的命令列。
* *parser_type*：目前唯一支援的值是 `"Windows"` ，它會以與[CommandLineToArgvW](https://docs.microsoft.com/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw)相同的方式剖析命令列。

## <a name="returns"></a>傳回

命令列引數的動態陣列。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print parse_command_line("echo \"hello world!\"", "windows")
```

|結果|
|---|
|["echo"，"hello world！"]|
