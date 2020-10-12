---
title: 'parse_ipv4 ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 parse_ipv4。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 48bfab2549da572efba117c21d783b35ac0202af
ms.sourcegitcommit: 6f610cd9c56dbfaff4eb0470ac0d1441211ae52d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91954717"
---
# <a name="parse_ipv4"></a>parse_ipv4()

將 IPv4 字串轉換為 long (簽署的64位) 數位標記法。

```kusto
parse_ipv4("127.0.0.1") == 2130706433
parse_ipv4('192.1.168.1') < parse_ipv4('192.1.168.2') == true
```

## <a name="syntax"></a>語法

`parse_ipv4(`*`Expr`*`)`

## <a name="arguments"></a>引數

* *`Expr`*：代表將轉換為 long 之 IPv4 的字串運算式。 字串可包含使用 [IP 首碼標記法](#ip-prefix-notation)的網路遮罩。

## <a name="ip-prefix-notation"></a>IP 首碼標記法

您可以 `IP-prefix notation` 使用斜線 () 字元來定義 IP 位址 `/` 。
斜線 () 左邊的 IP 位址 `/` 是基底 IP 位址。 斜線 (/) 右邊的 (1 到 32) 數目是網路遮罩中連續1位的數目。

例如，192.168.2.0/24 會有相關聯的 net/subnetmask，其中包含以點分隔的十進位格式的24個連續位或255.255.255.0。

## <a name="returns"></a>傳回

如果轉換成功，結果將會是長數值。
如果轉換不成功，結果將會是 `null` 。
 
## <a name="example"></a>範例

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
