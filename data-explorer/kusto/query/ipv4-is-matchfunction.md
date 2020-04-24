---
title: ipv4_is_match() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 ipv4_is_match()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: aa0646321af2d467c1e4af07fba81ccdc58eff37
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513731"
---
# <a name="ipv4_is_match"></a>ipv4_is_match()

匹配兩個 IPv4 字串。

```kusto
ipv4_is_match("127.0.0.1", "127.0.0.1") == true
ipv4_is_match('192.168.1.1', '192.168.1.255') == false
ipv4_is_match('192.168.1.1/24', '192.168.1.255/24') == true
ipv4_is_match('192.168.1.1', '192.168.1.255', 24) == true
```

**語法**

`ipv4_is_match(`*Expr1*`, `*Expr2*`[ ,`*首碼遮罩*`])`

**引數**

* *Expr1*, *Expr2*: 表示 IPv4 位址的字串運算式。 可以使用[IP 首碼表示法](#ip-prefix-notation)遮罩 IPv4 字串。
* *首碼遮罩*:從 0 到 32 的整數,表示考慮的最顯著位數。

### <a name="ip-prefix-notation"></a>IP 前置表示法

使用`IP-prefix notation`斜杠`/`( ) 字元定義 IP 位址是常見做法。 斜杠`/`( ) LEFT 的 IP 位址是基本`/`IP 位址,斜杠 () 右側的數位(1 到 32)是網掩碼中連續 1 位的數量。 

示例:192.168.2.0/24 將具有一個關聯的網路/子網掩碼,其中包含 24 個連續位或 255.255.255.0,以點狀小數格式顯示。

**傳回**

在計算從參數前置的 IP 前置碼和可選`PrefixMask`參數時,將分析和比較兩個 IPv4 字串。

傳回：
* `true`:如果第一個 IPv4 字串參數的長表示形式等於第二個 IPv4 字串參數。
*  `false`:否則。

如果兩個 IPv4 字串之一的轉換不成功,則結果將`null`為 。

**範例**

## <a name="ipv4-comparison-equality-cases"></a>IPv4 比較相等情況

下面的範例使用 IPv4 字串中指定的 IP 首碼表示法比較各種 IP。

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
|192.168.1.1/24|192.168.1.255|1|
|192.168.1.1|192.168.1.255/24|1|
|192.168.1.1/30|192.168.1.255/24|1|

下面的範例使用在IPv4字串中指定的IP首碼表示法作為`ipv4_is_match()`函數的其他參數比較各種IP。

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
|192.168.1.1/24|192.168.1.255|31|1|
|192.168.1.1|192.168.1.255|24|1|