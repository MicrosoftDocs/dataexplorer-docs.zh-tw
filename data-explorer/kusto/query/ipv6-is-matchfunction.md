---
title: ipv6_is_match （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 ipv6_is_match （）函數。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: b6d76f8ed834ec40c53321644e5cd9b7f5f93168
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347301"
---
# <a name="ipv6_is_match"></a>ipv6_is_match()

符合兩個 IPv6 或 IPv4 網路位址字串。 這兩個 IPv6/IPv4 字串會經過剖析和比較，同時會計入引數前置詞所計算的結合 IP 首碼遮罩，以及選擇性的 `PrefixMask` 引數。

```kusto
ipv6_is_match('::ffff:7f00:1', '127.0.0.1') == true
ipv6_is_match('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995') == false
ipv6_is_match('192.168.1.1/24', '192.168.1.255/24') == true
ipv6_is_match('fe80::85d:e82c:9446:7994/127', 'fe80::85d:e82c:9446:7995/127') == true
ipv6_is_match('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995', 127) == true
```

## <a name="syntax"></a>語法

`ipv6_is_match(`*運算式 1* `, `*運算式 2* `[ ,`*PrefixMask*`])`

## <a name="arguments"></a>引數

* *運算式 1*、運算式*2*：代表 IPv6 或 IPv4 位址的字串運算式。 IPv6 和 IPv4 字串可以使用[IP 首碼標記法](#ip-prefix-notation)來進行遮罩。
* *PrefixMask*：介於0到128之間的整數，代表所考慮的最高有效位數。

## <a name="ip-prefix-notation"></a>IP 首碼標記法
 
您可以 `IP-prefix notation` 使用斜線（）字元來定義 IP 位址 `/` 。
斜線（）左邊的 IP 位址 `/` 是基底 ip 位址。 斜線（）右邊的數位（1到127） `/` 是網路遮罩中連續1位的數目。 

## <a name="example"></a>範例：
fe80：：85d： e82c：9446： 7994/120 將會有相關聯的 net/subnetmask，其中包含120的連續位。

## <a name="returns"></a>傳回

* `true`：如果第一個 IPv6/IPv4 字串引數的長標記法等於第二個 IPv6/IPv4 字串引數。
* `false`別的.
* `null`：如果兩個 IPv6/IPv4 字串其中之一的轉換不成功，則為。

> [!Note]
> 函式可以接受並比較代表 IPv6 和 IPv4 網路位址的引數。 如果呼叫者知道引數為 IPv4 格式，請使用[ipv4_is_match （）](./ipv4-is-matchfunction.md)函數。 此函式會產生較佳的執行時間效能。

## <a name="examples"></a>範例

### <a name="ipv6ipv4-comparison-equality-case---ip-prefix-notation-specified-inside-the-ipv6ipv4-strings"></a>IPv6/IPv4 比較相等案例-IPv6/IPv4 字串內指定的 IP 首碼標記法

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string)
[
 // IPv4 are compared as IPv6 addresses
 '192.168.1.1',    '192.168.1.1',       // Equal IPs
 '192.168.1.1/24', '192.168.1.255',     // 24 bit IP4-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255/24',  // 24 bit IP4-prefix is used for comparison
 '192.168.1.1/30', '192.168.1.255/24',  // 24 bit IP4-prefix is used for comparison
  // IPv6 cases
 'fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7994',         // Equal IPs
 'fe80::85d:e82c:9446:7994/120', 'fe80::85d:e82c:9446:7998',     // 120 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7998/120',     // 120 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994/120', 'fe80::85d:e82c:9446:7998/120', // 120 bit IP6-prefix is used for comparison
 // Mixed case of IPv4 and IPv6
 '192.168.1.1',      '::ffff:c0a8:0101', // Equal IPs
 '192.168.1.1/24',   '::ffff:c0a8:01ff', // 24 bit IP-prefix is used for comparison
 '::ffff:c0a8:0101', '192.168.1.255/24', // 24 bit IP-prefix is used for comparison
 '::192.168.1.1/30', '192.168.1.255/24', // 24 bit IP-prefix is used for comparison
]
| extend result = ipv6_is_match(ip1_string, ip2_string)
```

|ip1_string|ip2_string|result|
|---|---|---|
|192.168.1.1|192.168.1.1|1|
|192.168.1.1/24|192.168.1.255 裝置|1|
|192.168.1.1|192.168.1.255 裝置/24|1|
|192.168.1.1/30|192.168.1.255 裝置/24|1|
|fe80：：85d： e82c：9446：7994|fe80：：85d： e82c：9446：7994|1|
|fe80：：85d： e82c：9446： 7994/120|fe80：：85d： e82c：9446：7998|1|
|fe80：：85d： e82c：9446：7994|fe80：：85d： e82c：9446： 7998/120|1|
|fe80：：85d： e82c：9446： 7994/120|fe80：：85d： e82c：9446： 7998/120|1|
|192.168.1.1|：： ffff： c0a8：0101|1|
|192.168.1.1/24|：： ffff： c0a8：01ff|1|
|：： ffff： c0a8：0101|192.168.1.255 裝置/24|1|
|：： 192.168.1.1/30|192.168.1.255 裝置/24|1|


### <a name="ipv6ipv4-comparison-equality-case--ip-prefix-notation-specified-inside-the-ipv6ipv4-strings-and-as-additional-argument-of-the-ipv6_is_match-function"></a>IPv6/IPv4 比較相等案例-IPv6/IPv4 字串內指定的 IP 首碼標記法，以及做為函式 `ipv6_is_match()` 的其他引數

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string, prefix:long)
[
 // IPv4 are compared as IPv6 addresses 
 '192.168.1.1',    '192.168.1.0',   31, // 31 bit IP4-prefix is used for comparison
 '192.168.1.1/24', '192.168.1.255', 31, // 24 bit IP4-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255', 24, // 24 bit IP4-prefix is used for comparison
   // IPv6 cases
 'fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995',     127, // 127 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994/127', 'fe80::85d:e82c:9446:7998', 120, // 120 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994/120', 'fe80::85d:e82c:9446:7998', 127, // 120 bit IP6-prefix is used for comparison
 // Mixed case of IPv4 and IPv6
 '192.168.1.1/24',   '::ffff:c0a8:01ff', 127, // 127 bit IP6-prefix is used for comparison
 '::ffff:c0a8:0101', '192.168.1.255',    120, // 120 bit IP6-prefix is used for comparison
 '::192.168.1.1/30', '192.168.1.255/24', 127, // 120 bit IP6-prefix is used for comparison
]
| extend result = ipv6_is_match(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|prefix|result|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|1|
|192.168.1.1/24|192.168.1.255 裝置|31|1|
|192.168.1.1|192.168.1.255 裝置|24|1|
|fe80：：85d： e82c：9446：7994|fe80：：85d： e82c：9446：7995|127|1|
|fe80：：85d： e82c：9446： 7994/127|fe80：：85d： e82c：9446：7998|120|1|
|fe80：：85d： e82c：9446： 7994/120|fe80：：85d： e82c：9446：7998|127|1|
|192.168.1.1/24|：： ffff： c0a8：01ff|127|1|
|：： ffff： c0a8：0101|192.168.1.255 裝置|120|1|
|：： 192.168.1.1/30|192.168.1.255 裝置/24|127|1|
