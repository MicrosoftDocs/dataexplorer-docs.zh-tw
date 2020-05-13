---
title: parse_ipv4 （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 parse_ipv4 （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 2bfea891b6057b5d43b65fa045e2b01d5e025a82
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271242"
---
# <a name="parse_ipv4"></a>parse_ipv4()

將 IPv4 字串轉換為 long （帶正負號的64位）數位表示。

```kusto
parse_ipv4("127.0.0.1") == 2130706433
parse_ipv4('192.1.168.1') < parse_ipv4('192.1.168.2') == true
```

**語法**

`parse_ipv4(`*Expr*`)`

**引數**

* *Expr*：代表將轉換成 Long 的 IPv4 的字串運算式。 字串可能包含使用[IP 首碼標記法](#ip-prefix-notation)的網路遮罩。

### <a name="ip-prefix-notation"></a>IP 首碼標記法

使用 `IP-prefix notation` 斜線（）字元來定義 IP 位址是常見的作法 `/` 。
斜線（）左邊的 IP 位址 `/` 是基底 IP 位址，而斜線（/）右邊的數位（1到32）是網路遮罩中連續1位的數目。 因此，192.168.2.0/24 會有相關聯的 net/subnetmask，其中包含小數點十進位格式的24個連續位或255.255.255.0。

**傳回**

如果轉換成功，則結果會是長數位。
如果轉換不成功，則結果會是 `null` 。
 
**範例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip_string:string)
[
 '192.168.1.1',
 '192.168.1.1/24',
 '255.255.255.255/31'
]
| extend ip_long = parse_ipv4(ip_string)
```

|ip_string|ip_long|
|---|---|
|192.168.1.1|3232235777|
|192.168.1.1/24|3232235776|
|255.255.255.255/31|4294967294|
