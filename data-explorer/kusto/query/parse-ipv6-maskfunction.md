---
title: parse_ipv4_mask （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 parse_ipv6_mask （）函數。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 99618c4d171e8ccade39ce50b9a0d46f9769f5f8
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/02/2020
ms.locfileid: "84301226"
---
# <a name="parse_ipv6_mask"></a>parse_ipv6_mask （）
 
將 IPv6/IPv4 字串和網路遮罩轉換成標準 IPv6 字串表示。

```kusto
parse_ipv6_mask("127.0.0.1", 24) == '0000:0000:0000:0000:0000:ffff:7f00:0000'
parse_ipv6_mask(":fe80::85d:e82c:9446:7994", 120) == 'fe80:0000:0000:0000:085d:e82c:9446:7900'
```

**語法**

`parse_ipv6_mask(`*`Expr`*`, `*`PrefixMask`*`)`

**引數**

* *`Expr`*：字串運算式，代表將轉換成標準 IPv6 標記法的 IPv6/IPv4 網路位址。 字串可能包含使用[IP 首碼標記法](#ip-prefix-notation)的網路遮罩。
* *`PrefixMask`*：介於0到128之間的整數，代表所考慮的最高有效位數。

## <a name="ip-prefix-notation"></a>IP 首碼標記法

您可以 `IP-prefix notation` 使用斜線（）字元來定義 IP 位址 `/` 。
斜線（）左邊的 IP 位址 `/` 是基底 ip 位址。 斜線（）右邊的數位（1到127） `/` 是網路遮罩中連續1位的數目。

**傳回**

如果轉換成功，則結果會是代表標準 IPv6 網路位址的字串。
如果轉換不成功，則結果會是 `null` 。

**範例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip_string:string, netmask:long)
[
 // IPv4 addresses
 '192.168.255.255',     120,  // 120-bit netmask is used
 '192.168.255.255/24',  124,  // 120-bit netmask is used, as IPv4 address doesn't use upper 8 bits
 '255.255.255.255', 128,  // 128-bit netmask is used
 // IPv6 addresses
 'fe80::85d:e82c:9446:7994', 128,     // 128-bit netmask is used
 'fe80::85d:e82c:9446:7994/120', 124, // 120-bit netmask is used
 // IPv6 with IPv4 notation
 '::192.168.255.255',    128,  // 128-bit netmask is used
 '::192.168.255.255/24', 128,  // 120-bit netmask is used, as IPv4 address doesn't use upper 8 bits
]
| extend ip6_canonical = parse_ipv6_mask(ip_string, netmask)
```

|ip_string|網路遮罩|ip6_canonical|
|---|---|---|
|192.168.255.255|120|0000：0000：0000：0000：0000： ffff： c0a8： ff00|
|192.168.255.255/24|124|0000：0000：0000：0000：0000： ffff： c0a8： ff00|
|255.255.255.255|128|0000：0000：0000：0000：0000： ffff： ffff： ffff|
|fe80：：85d： e82c：9446：7994|128|fe80：0000：0000：0000：085d： e82c：9446：7994|
|fe80：：85d： e82c：9446： 7994/120|124|fe80：0000：0000：0000：085d： e82c：9446：7900|
|：：192.168.255.255|128|0000：0000：0000：0000：0000： ffff： c0a8： ffff|
|：： 192.168.255.255/24|128|0000：0000：0000：0000：0000： ffff： c0a8： ff00|

## <a name="next-steps"></a>後續步驟

如需其他類似的功能，請參閱：

* [parse_ipv4_mask()](parse-ipv4-maskfunction.md)
* [parse_ipv6 （）](parse-ipv6function.md)
