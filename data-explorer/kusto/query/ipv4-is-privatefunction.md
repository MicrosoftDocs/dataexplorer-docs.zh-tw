---
title: 'ipv4_is_private ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中 ( # A1 函式的 ipv4_is_private。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/20/2020
ms.openlocfilehash: 161e0a2ca94bb9b037fc619c0fc5dfbddd8d28da
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324889"
---
# <a name="ipv4_is_private"></a>ipv4_is_private ( # A1

檢查 IPv4 字串位址是否屬於一組私人網路 Ip。

[私人網路位址](https://en.wikipedia.org/wiki/Private_network) 原本是為了協助延遲 IPv4 位址耗盡而定義的。 源自或定址至私人 IP 位址的 IP 封包，無法透過公用網際網路路由傳送。

## <a name="private-ipv4-addresses"></a>私人 IPv4 位址

網際網路工程任務推動小組 (IETF) 已將網際網路指派的號碼授權單位 (IANA) 為私人網路保留下列 IPv4 位址範圍：

| IP 位址範圍|位址數目|最大的 CIDR 區塊 (子網路遮罩) |
|-----------------|-------------------|--------------------------------|
|10.0.0.0 – 10.255.255.255|16777216|10.0.0.0/8 (255.0.0.0) |
|172.16.0.0 – 172.31.255.255|1048576|172.16.0.0/12 (255.240.0.0) |
|192.168.0.0 – 192.168.255.255|65536|192.168.0.0/16 (255.255.0.0) |


```kusto
ipv4_is_private('192.168.1.1/24') == true
ipv4_is_private('10.1.2.3/24') == true
ipv4_is_private('202.1.2.3') == false
ipv4_is_private("127.0.0.1") == false
```

## <a name="syntax"></a>Syntax

`ipv4_is_private(`*Expr*`)`

## <a name="arguments"></a>引數

*Expr*：代表 IPv4 位址的字串運算式。 您可以使用 [IP 首碼標記法](#ip-prefix-notation)來遮罩 IPv4 字串。

### <a name="ip-prefix-notation"></a>IP 首碼標記法

您可以 `IP-prefix notation` 使用斜線 () 字元來定義 IP 位址 `/` 。 斜線 () 左邊的 IP 位址 `/` 是基底 IP 位址。  (斜線的右邊 (1 到 32) `/`) 是網路遮罩中連續1位的數目。 

例如，192.168.2.0/24 會有相關聯的 net/subnetmask，其中包含以點分隔的十進位格式的24個連續位或255.255.255.0。

## <a name="returns"></a>傳回

* `true`：如果 IPv4 位址屬於任何私人網路範圍，則為。
*  `false`相反.
* `null`：如果兩個 IPv4 字串的其中一個無法成功轉換，則為。

## <a name="example-check-if-ipv4-belongs-to-a-private-network"></a>範例：檢查 IPv4 是否屬於私人網路

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip_string:string)
[
 '10.1.2.3',
 '192.168.1.1/24',
 '127.0.0.1',
]
| extend result = ipv4_is_private(ip_string)
```

|ip_string|result|
|---|---|
|10.1.2.3|1|
|192.168.1.1/24|1|
|127.0.0.1|0|
