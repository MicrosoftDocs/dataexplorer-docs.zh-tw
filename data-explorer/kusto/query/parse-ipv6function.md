---
title: parse_ipv6 （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 parse_ipv6 （）函數。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: 25ed06f738e6b2e090ff92be9df026a85a27a89f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346417"
---
# <a name="parse_ipv6"></a>parse_ipv6()

將 IPv6 或 IPv4 字串轉換成標準 IPv6 字串表示。

```kusto
parse_ipv6("127.0.0.1") == '0000:0000:0000:0000:0000:ffff:7f00:0001'
parse_ipv6(":fe80::85d:e82c:9446:7994") == 'fe80:0000:0000:0000:085d:e82c:9446:7994'
```

## <a name="syntax"></a>語法

`parse_ipv6(`*`Expr`*`)`

## <a name="arguments"></a>引數

* *`Expr`*：字串運算式，代表將轉換成標準 IPv6 標記法的 IPv6/IPv4 網路位址。 字串可能包含使用[IP 首碼標記法](#ip-prefix-notation)的網路遮罩。

## <a name="ip-prefix-notation"></a>IP 首碼標記法

您可以 `IP-prefix notation` 使用斜線（）字元來定義 IP 位址 `/` 。
斜線（）左邊的 IP 位址 `/` 是基底 ip 位址。 斜線（）右邊的數位（1到127） `/` 是網路遮罩中連續1位的數目。

## <a name="returns"></a>傳回

如果轉換成功，則結果會是代表標準 IPv6 網路位址的字串。
如果轉換不成功，則結果會是 `null` 。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip_string:string, netmask:long)
[
 '192.168.255.255',     32,  // 32-bit netmask is used
 '192.168.255.255/24',  30,  // 24-bit netmask is used, as IPv4 address doesn't use upper 8 bits
 '255.255.255.255',     24,  // 24-bit netmask is used
]
| extend ip_long = parse_ipv4_mask(ip_string, netmask)
```

|ip_string|網路遮罩|ip_long|
|---|---|---|
|192.168.255.255|32|3232301055|
|192.168.255.255/24|30|3232300800|
|255.255.255.255|24|4294967040|


