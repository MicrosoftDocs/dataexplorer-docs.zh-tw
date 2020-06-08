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
ms.openlocfilehash: b0377cd8af302d2680c0ee451d05f4b4b083ccec
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512600"
---
# <a name="parse_ipv4"></a>parse_ipv4()

將 IPv4 字串轉換為 long （帶正負號的64位）數位表示。

```kusto
parse_ipv4("127.0.0.1") == 2130706433
parse_ipv4('192.1.168.1') < parse_ipv4('192.1.168.2') == true
```

**語法**

`parse_ipv4(`*`Expr`*`)`

**引數**

* *`Expr`*：字串運算式，代表將轉換成 long 的 IPv4。 字串可能包含使用[IP 首碼標記法](#ip-prefix-notation)的網路遮罩。

## <a name="ip-prefix-notation"></a>IP 首碼標記法

您可以 `IP-prefix notation` 使用斜線（）字元來定義 IP 位址 `/` 。
斜線（）左邊的 IP 位址 `/` 是基底 ip 位址。 斜線（/）右邊的數位（1到32）是網路遮罩中連續1位的數目。

**範例**

192.168.2.0/24 會有相關聯的 net/subnetmask，其中包含小數點十進位格式的24個連續位或255.255.255.0。

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
