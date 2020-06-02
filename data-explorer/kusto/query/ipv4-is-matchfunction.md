---
title: ipv4_is_match （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 ipv4_is_match （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 7b076f9878828ca8503c808b6ab94daf3375e2d4
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294622"
---
# <a name="ipv4_is_match"></a>ipv4_is_match （）

符合兩個 IPv4 字串。 這兩個 IPv4 字串會經過剖析和比較，同時會計入引數前置詞所計算的結合 IP 首碼遮罩和選擇性 `PrefixMask` 引數。

```kusto
ipv4_is_match("127.0.0.1", "127.0.0.1") == true
ipv4_is_match('192.168.1.1', '192.168.1.255') == false
ipv4_is_match('192.168.1.1/24', '192.168.1.255/24') == true
ipv4_is_match('192.168.1.1', '192.168.1.255', 24) == true
```

**語法**

`ipv4_is_match(`*運算式 1* `, `*運算式 2* `[ ,`*PrefixMask*`])`

**引數**

* *運算式 1*、運算式*2*：代表 IPv4 位址的字串運算式。 IPv4 字串可以使用[IP 首碼標記法](#ip-prefix-notation)來進行遮罩。
* *PrefixMask*：介於0到32之間的整數，代表所考慮的最高有效位數。

## <a name="ip-prefix-notation"></a>IP 首碼標記法

您可以 `IP-prefix notation` 使用斜線（）字元來定義 IP 位址 `/` 。 斜線（）左邊的 IP 位址 `/` 是基底 ip 位址。 斜線（）右邊的數位（1到32） `/` 是網路遮罩中連續1位的數目。 
**範例：** 192.168.2.0/24 會有相關聯的 net/subnetmask，其中包含小數點十進位格式的24個連續位或255.255.255.0。

**傳回**

* `true`：如果第一個 IPv4 字串引數的長標記法等於第二個 IPv4 字串引數。
*  `false`別的.
* `null`：如果兩個 IPv4 字串其中之一的轉換不成功，則為。

## <a name="examples"></a>範例

### <a name="ipv4-comparison-equality---ip-prefix-notation-specified-inside-the-ipv4-strings"></a>IPv4 比較是否相等-IPv4 字串內指定的 IP 首碼標記法。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string)
[
 '192.168.1.0',    '192.168.1.0',       // Equal IPs
 '192.168.1.1/24', '192.168.1.255',     // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
 '192.168.1.1/30', '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_is_match(ip1_string, ip2_string)
```

|ip1_string|ip2_string|result|
|---|---|---|
|192.168.1.0|192.168.1.0|1|
|192.168.1.1/24|192.168.1.255 裝置|1|
|192.168.1.1|192.168.1.255 裝置/24|1|
|192.168.1.1/30|192.168.1.255 裝置/24|1|

### <a name="ipv4-comparison-equality---ip-prefix-notation-specified-inside-the-ipv4-strings-and-an-additional-argument-of-the-ipv4_is_match-function"></a>IPv4 比較是否相等-IPv4 字串內指定的 IP 首碼標記法，以及函式的其他引數 `ipv4_is_match()`

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string, prefix:long)
[
 '192.168.1.1',    '192.168.1.0',   31, // 31 bit IP-prefix is used for comparison
 '192.168.1.1/24', '192.168.1.255', 31, // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255', 24, // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_is_match(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|prefix|result|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|1|
|192.168.1.1/24|192.168.1.255 裝置|31|1|
|192.168.1.1|192.168.1.255 裝置|24|1|
