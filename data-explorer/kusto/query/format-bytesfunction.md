---
title: format_bytes （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 format_bytes （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4da07433be5a052d71740931d4dedd9df0399f56
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227312"
---
# <a name="format_bytes"></a>format_bytes()

將數位格式化為表示資料大小（以位元組為單位）的字串。

```kusto
format_bytes(1024) == '1 KB'"
```

**語法**

`format_bytes(`*值*[ `,` *精確度*[ `,` *單位*]]`)`

**引數**

* `value`：要格式化為資料大小（以位元組為單位）的數位。
* `precision`：（選擇性）值將四捨五入的位數。 （預設值為0）。
* `units`：（選擇性）字串格式將使用的目標資料大小單位（ `Bytes` 、 `KB` 、 `MB` 、 `GB` 、 `TB` 、 `PB` ）。 如果參數是空的，則會根據輸入值自動選取單位。

**傳回**

具有格式結果的字串。

**範例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print 
v1 = format_bytes(564),
v2 = format_bytes(10332, 1),
v3 = format_bytes(20010332),
v4 = format_bytes(20010332, 2),
v5 = format_bytes(20010332, 0, "KB")
```

|v1|v2|v3|v4|v5|
|---|---|---|---|---|
|564個位元組|10.1 KB|19 MB|19.08 MB|19541 KB|
