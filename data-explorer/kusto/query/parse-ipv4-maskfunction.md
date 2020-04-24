---
title: parse_ipv4_mask() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的parse_ipv4_mask()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 994c1e551d7c38bb1493579bcf10438acd2923f2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511776"
---
# <a name="parse_ipv4_mask"></a>parse_ipv4_mask()

將 IPv4 和網遮罩的輸入字串轉換為長數表示形式(簽名 64 位元)。

```kusto
parse_ipv4_mask("127.0.0.1", 24) == 2130706432

parse_ipv4_mask('192.1.168.2', 31) == parse_ipv4_mask('192.1.168.3', 31) 
```

**語法**

`parse_ipv4_mask(`*Expr*`, `*前置碼*`)`

**引數**

* *Expr*: IPv4 位址的字串表示形式,將轉換為長。 
* *首碼遮罩*:從 0 到 32 的整數,表示考慮的最顯著位數。

**傳回**

如果轉換成功,結果將是一個長數。
如果轉換不成功,結果會為`null`。