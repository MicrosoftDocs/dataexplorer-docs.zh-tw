---
title: parse_ipv4() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的parse_ipv4()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 296c7e77a2397501ae086e16154ca249249c23e4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511742"
---
# <a name="parse_ipv4"></a>parse_ipv4()

將 IPv4 字串轉換為長(簽名 64 位)數位表示形式。

```kusto
parse_ipv4("127.0.0.1") == 2130706433
parse_ipv4('192.1.168.1') < parse_ipv4('192.1.168.2') == true
```

**語法**

`parse_ipv4(`*Expr*`)`

**引數**

* *Expr*: 表示將轉換為長的 IPv4 的字串表示式。 字串可能包括使用[IP 首碼表示法](#ip-prefix-notation)的淨掩碼。

### <a name="ip-prefix-notation"></a>IP 前置表示法

使用`IP-prefix notation`斜杠`/`( ) 字元定義 IP 位址是常見做法。
斜杠`/`( ) LEFT 的 IP 位址是基本 IP 位址,斜杠 (/) 右側的數位(1 到 32)是網掩碼中連續 1 位的數量。 因此,192.168.2.0/24 將具有一個關聯的網路/子網掩碼,其中包含24個連續位或255.255.255.0的虛線小數格式。

**傳回**

如果轉換成功,結果將是一個長數。
如果轉換不成功,結果會為`null`。
 
**範例**

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