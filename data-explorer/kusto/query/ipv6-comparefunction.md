---
title: 'ipv6_compare ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中的 ipv6_compare ( # A1 函數。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: ce14c466d927dd2431ae8e057bceec0d5fdd38b8
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803891"
---
# <a name="ipv6_compare"></a>ipv6_compare()

比較兩個 IPv6 或 IPv4 網路位址字串。 這兩個 IPv6 字串會經過剖析和比較，同時會計入引數前置詞所計算的結合 IP 首碼遮罩和選擇性 `PrefixMask` 引數。

```kusto
ipv6_compare('::ffff:7f00:1', '127.0.0.1') == 0
ipv6_compare('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995')  < 0
ipv6_compare('192.168.1.1/24', '192.168.1.255/24') == 0
ipv6_compare('fe80::85d:e82c:9446:7994/127', 'fe80::85d:e82c:9446:7995/127') == 0
ipv6_compare('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995', 127) == 0
```

> [!Note]
> 函式可以接受並比較代表 IPv6 和 IPv4 網路位址的引數。 不過，如果呼叫者知道引數為 IPv4 格式，請使用[ipv4_is_compare ( # B1](./ipv4-comparefunction.md)函數。 此函式會產生較佳的執行時間效能。

## <a name="syntax"></a>語法

`ipv6_compare(`*運算式 1* `, `*運算式 2* `[ ,`*PrefixMask*`])`

## <a name="arguments"></a>引數

* *運算式 1*、運算式*2*：代表 IPv6 或 IPv4 位址的字串運算式。 IPv6 和 IPv4 字串可以使用 IP 首碼標記法加以遮罩 (請參閱附注) 。
* *PrefixMask*：介於0到128之間的整數，代表所考慮的最高有效位數。

## <a name="ip-prefix-notation"></a>IP 首碼標記法

`IP-prefix notation`使用斜線 () 字元來定義 IP 位址是常見的作法 `/` 。
斜線 (的左邊的 IP 位址 `/`) 是基底 IP 位址，而斜線的右邊 (1 到 127) 的數位 `/` (是網路遮罩中連續1位的數目。 

例如，fe80：：85d： e82c：9446： 7994/120 將會有相關聯的 net/subnetmask，其中包含120的連續位。

## <a name="returns"></a>傳回

* `0`：如果第一個 IPv6 字串引數的長標記法等於第二個 IPv6 字串引數。
* `1`：如果第一個 IPv6 字串引數的長表示大於第二個 IPv6 字串引數。
* `-1`：如果第一個 IPv6 字串引數的長表示小於第二個 IPv6 字串引數。
* `null`：如果兩個 IPv6 字串其中之一的轉換不成功，則為。

## <a name="examples-ipv6ipv4-comparison-equality-cases"></a>範例： IPv6/IPv4 比較相等案例

### <a name="compare-ips-using-the-ip-prefix-notation-specified-inside-the-ipv6ipv4-strings"></a>使用 IPv6/IPv4 字串內指定的 IP 首碼標記法來比較 Ip

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
| extend result = ipv6_compare(ip1_string, ip2_string)
```

|ip1_string|ip2_string|result|
|---|---|---|
|192.168.1.1|192.168.1.1|0|
|192.168.1.1/24|192.168.1.255 裝置|0|
|192.168.1.1|192.168.1.255 裝置/24|0|
|192.168.1.1/30|192.168.1.255 裝置/24|0|
|fe80：：85d： e82c：9446：7994|fe80：：85d： e82c：9446：7994|0|
|fe80：：85d： e82c：9446： 7994/120|fe80：：85d： e82c：9446：7998|0|
|fe80：：85d： e82c：9446：7994|fe80：：85d： e82c：9446： 7998/120|0|
|fe80：：85d： e82c：9446： 7994/120|fe80：：85d： e82c：9446： 7998/120|0|
|192.168.1.1|：： ffff： c0a8：0101|0|
|192.168.1.1/24|：： ffff： c0a8：01ff|0|
|：： ffff： c0a8：0101|192.168.1.255 裝置/24|0|
|：： 192.168.1.1/30|192.168.1.255 裝置/24|0|

### <a name="compare-ips-using-ip-prefix-notation-specified-inside-the-ipv6ipv4-strings-and-as-additional-argument-of-the-ipv6_compare-function"></a>使用 IPv6/IPv4 字串內指定的 IP 首碼標記法，以及做為函式的其他引數，來比較 Ip `ipv6_compare()`

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
| extend result = ipv6_compare(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|prefix|result|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|0|
|192.168.1.1/24|192.168.1.255 裝置|31|0|
|192.168.1.1|192.168.1.255 裝置|24|0|
|fe80：：85d： e82c：9446：7994|fe80：：85d： e82c：9446：7995|127|0|
|fe80：：85d： e82c：9446： 7994/127|fe80：：85d： e82c：9446：7998|120|0|
|fe80：：85d： e82c：9446： 7994/120|fe80：：85d： e82c：9446：7998|127|0|
|192.168.1.1/24|：： ffff： c0a8：01ff|127|0|
|：： ffff： c0a8：0101|192.168.1.255 裝置|120|0|
|：： 192.168.1.1/30|192.168.1.255 裝置/24|127|0|

