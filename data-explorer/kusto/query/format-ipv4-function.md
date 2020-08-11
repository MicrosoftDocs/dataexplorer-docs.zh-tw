---
title: 'format_ipv4 ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中的 format_ipv4 ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/09/2020
ms.openlocfilehash: 5f941a640ffc0bf30859509cf3e3eb1235f464ff
ms.sourcegitcommit: ed902a5a781e24e081cd85910ed15cd468a0db1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88075381"
---
# <a name="format_ipv4"></a>format_ipv4 ( # A1

使用網路遮罩剖析輸入，並傳回代表 IPv4 位址的字串。

```kusto
print format_ipv4('192.168.1.255', 24) == '192.168.1.0'
print format_ipv4(3232236031, 24) == '192.168.1.0'
```

## <a name="syntax"></a>語法

`format_ipv4(`*Expr* [ `,` *PrefixMask*`])`

## <a name="arguments"></a>引數

* *`Expr`*： IPv4 位址的字串或數位標記法。
* *`PrefixMask`*： (選擇性) 從0到32的整數，代表所考慮的最高有效位數。 如果未指定引數，則會使用所有位元遮罩 (32) 。

## <a name="returns"></a>傳回

如果轉換成功，則結果會是代表 IPv4 位址的字串。
如果轉換不成功，則結果會是空字串。

## <a name="see-also"></a>另請參閱

- [format_ipv4_mask ( # B1 ](format-ipv4-mask-function.md)：適用于包含 CIDR 標記法的 ipv4 位址格式。
- [IPv4 和 IPv6 函式](scalarfunctions.md#ipv4ipv6-functions)

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(address:string, mask:long)
[
 '192.168.1.1', 24,          
 '192.168.1.1', 32,          
 '192.168.1.1/24', 32,       
 '192.168.1.1/24', long(-1), 
]
| extend result = format_ipv4(address, mask), 
         result_mask = format_ipv4_mask(address, mask)
```

|address|遮罩|result|result_mask|
|---|---|---|---|
|192.168.1.1|24|192.168.1.0|192.168.1.0/24|
|192.168.1.1|32|192.168.1.1|192.168.1.1/32|
|192.168.1.1/24|32|192.168.1.0|192.168.1.0/24|
|192.168.1.1/24|-1|||
