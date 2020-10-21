---
title: 'format_bytes ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 format_bytes。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c324813ed53b57673f8962f87374eb998f2a3443
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244763"
---
# <a name="format_bytes"></a>format_bytes()

將數位格式化為代表資料大小（以位元組為單位）的字串。

```kusto
format_bytes(1024) == '1 KB'"
```

## <a name="syntax"></a>語法

`format_bytes(`*值* [ `,` *精確度* [ `,` *單位*]]`)`

## <a name="arguments"></a>引數

* `value`：要格式化為資料大小（以位元組為單位）的數位。
* `precision`： (值將四捨五入為的選擇性) 位數。  (預設值為 0) 。
* `units`： (選用的目標資料大小) 單位，字串格式將使用 (`Bytes` 、、 `KB` 、 `MB` 、 `GB` `TB` `PB`) 。 如果參數是空的，則會根據輸入值自動選取單位。

## <a name="returns"></a>傳回

具有格式結果的字串。

## <a name="examples"></a>範例

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
|564位元組|10.1 KB|19 MB|19.08 MB|19541 KB|
