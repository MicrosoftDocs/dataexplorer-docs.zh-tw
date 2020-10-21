---
title: 'ipv4_compare ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 ipv4_compare。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 06cb9078464d782d6034ec11e3e0cdc4c249b541
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250356"
---
# <a name="ipv4_compare"></a>ipv4_compare()

比較兩個 IPv4 字串。 在計算引數前置詞和選擇性引數的合併 IP 首碼遮罩時，會剖析並比較兩個 IPv4 字串 `PrefixMask` 。

```kusto
ipv4_compare("127.0.0.1", "127.0.0.1") == 0
ipv4_compare('192.168.1.1', '192.168.1.255') < 0
ipv4_compare('192.168.1.1/24', '192.168.1.255/24') == 0
ipv4_compare('192.168.1.1', '192.168.1.255', 24) == 0
```

## <a name="syntax"></a>語法

`ipv4_compare(`*運算式 1* `, `*運算式 2* `[ ,`*PrefixMask*`])`

## <a name="arguments"></a>引數

* *運算式 1*- *2*：代表 IPv4 位址的字串運算式。 您可以使用 [IP 首碼標記法](#ip-prefix-notation)來遮罩 IPv4 字串。
* *PrefixMask*：從0到32的整數，代表納入考慮的最高有效位數目。

## <a name="ip-prefix-notation"></a>IP 首碼標記法
 
您可以 `IP-prefix notation` 使用斜線 () 字元來定義 IP 位址 `/` 。
斜線 () 左邊的 IP 位址 `/` 是基底 IP 位址。  (斜線的右邊 (1 到 32) `/`) 是網路遮罩中連續1位的數目。 

例如，192.168.2.0/24 會有相關聯的 net/subnetmask，其中包含以點分隔的十進位格式的24個連續位或255.255.255.0。

## <a name="returns"></a>傳回

* `0`：如果第一個 IPv4 字串引數的完整標記法等於第二個 IPv4 字串引數
* `1`：如果第一個 IPv4 字串引數的長整數表示大於第二個 IPv4 字串引數
* `-1`：如果第一個 IPv4 字串引數的完整標記法小於第二個 IPv4 字串引數
* `null`：如果兩個 IPv4 字串的其中一個無法成功轉換，則為。

## <a name="examples-ipv4-comparison-equality-cases"></a>範例： IPv4 比較相等案例

### <a name="compare-ips-using-the-ip-prefix-notation-specified-inside-the-ipv4-strings"></a>使用 IPv4 字串內指定的 IP 首碼標記法來比較 Ip

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string)
[
 '192.168.1.0',    '192.168.1.0',       // Equal IPs
 '192.168.1.1/24', '192.168.1.255',     // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
 '192.168.1.1/30', '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_compare(ip1_string, ip2_string)
```

|ip1_string|ip2_string|result|
|---|---|---|
|192.168.1.0|192.168.1.0|0|
|192.168.1.1/24|192.168.1.255 裝置|0|
|192.168.1.1|192.168.1.255 裝置/24|0|
|192.168.1.1/30|192.168.1.255 裝置/24|0|

### <a name="compare-ips-using-ip-prefix-notation-specified-inside-the-ipv4-strings-and-as-additional-argument-of-the-ipv4_compare-function"></a>使用 IPv4 字串內指定的 IP 首碼標記法和函式的其他引數，比較 ip `ipv4_compare()`

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string, prefix:long)
[
 '192.168.1.1',    '192.168.1.0',   31, // 31 bit IP-prefix is used for comparison
 '192.168.1.1/24', '192.168.1.255', 31, // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255', 24, // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_compare(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|prefix|result|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|0|
|192.168.1.1/24|192.168.1.255 裝置|31|0|
|192.168.1.1|192.168.1.255 裝置|24|0|

