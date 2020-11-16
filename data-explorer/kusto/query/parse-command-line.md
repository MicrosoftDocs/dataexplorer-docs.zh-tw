---
title: 'parse_command_line ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 parse_command_line。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: slneimer
ms.service: data-explorer
ms.topic: reference
ms.date: 06/28/2020
ms.openlocfilehash: da8fe0bfedf5766f4599509e83fa1c7c050d069f
ms.sourcegitcommit: 88f8ad67711a4f614d65d745af699d013d01af32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2020
ms.locfileid: "94638983"
---
# <a name="parse_command_line"></a>parse_command_line()

剖析 Unicode 命令列字串，並傳回命令列引數的動態陣列。

## <a name="syntax"></a>語法

`parse_command_line(`*command_line* ， *parser_type*`)`

## <a name="arguments"></a>引數

* *command_line* ：要剖析的命令列。
* *parser_type* ：目前唯一支援的值是 `"windows"` ，它會以與 [CommandLineToArgvW](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw)相同的方式來剖析命令列。

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
