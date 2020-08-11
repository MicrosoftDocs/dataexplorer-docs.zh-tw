---
title: 'format_ipv4_mask ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中的 format_ipv4_mask ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/09/2020
ms.openlocfilehash: ef0b8306ad5d62e7efa9cb037a6fb8d06d415859
ms.sourcegitcommit: ed902a5a781e24e081cd85910ed15cd468a0db1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88075385"
---
# <a name="format_ipv4_mask"></a>format_ipv4_mask ( # A1

使用網路遮罩來剖析輸入，並傳回代表 IPv4 位址的字串做為 CIDR 標記法。

```kusto
print format_ipv4_mask('192.168.1.255', 24) == '192.168.1.0/24'
print format_ipv4_mask(3232236031, 24) == '192.168.1.0/24'
```

## <a name="syntax"></a>語法

`format_ipv4_mask(`*Expr* [ `,` *PrefixMask*`])`

## <a name="arguments"></a>引數

* *`Expr`*：以 CIDR 標記法表示的 IPv4 位址字串或數位。
* *`PrefixMask`*： (選擇性) 從0到32的整數，代表所考慮的最高有效位數。 如果未指定引數，則會使用所有位元遮罩 (32) 。

## <a name="returns"></a>傳回

如果轉換成功，則結果會是將 IPv4 位址表示為 CIDR 標記法的字串。
如果轉換不成功，則結果會是空字串。

## <a name="see-also"></a>另請參閱

- [format_ipv4 ( # B1 ](format-ipv4-function.md)：適用于不含 CIDR 標記法的 ipv4 位址格式。
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
