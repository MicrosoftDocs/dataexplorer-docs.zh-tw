---
title: parse_ipv4_mask （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 parse_ipv4_mask （）函數。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: 7399bf83369a1b1e1565dd127712faccf7e60830
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346468"
---
# <a name="parse_ipv4_mask"></a>parse_ipv4_mask()

將 IPv4 和網路遮罩的輸入字串轉換成長數位表示（帶正負號的64位）。

```kusto
parse_ipv4_mask("127.0.0.1", 24) == 2130706432
parse_ipv4_mask('192.1.168.2', 31) == parse_ipv4_mask('192.1.168.3', 31)
```

## <a name="syntax"></a>語法

`parse_ipv4_mask(`*`Expr`*`, `*`PrefixMask`*`)`

## <a name="arguments"></a>引數

* *`Expr`*：將轉換成 long 的 IPv4 位址的字串表示。 
* *`PrefixMask`*：介於0到32之間的整數，代表所考慮的最高有效位數。

## <a name="returns"></a>傳回

如果轉換成功，則結果會是長數位。
如果轉換不成功，則結果會是 `null` 。
