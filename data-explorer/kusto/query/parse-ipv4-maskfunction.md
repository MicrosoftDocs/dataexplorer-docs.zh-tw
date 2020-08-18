---
title: 'parse_ipv4_mask ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 函式 parse_ipv4_mask。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: c84a719c59f46702ba8f5d92db2a1277cef49fb4
ms.sourcegitcommit: 5e903c61e779f7bf62f745f13a6038ce2a32e934
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88512560"
---
# <a name="parse_ipv4_mask"></a>parse_ipv4_mask()

將 IPv4 和網路遮罩的輸入字串轉換為長數位表示 (簽署的64位) 。

```kusto
parse_ipv4_mask("127.0.0.1", 24) == 2130706432
parse_ipv4_mask('192.1.168.2', 31) == parse_ipv4_mask('192.1.168.3', 31)
```

## <a name="syntax"></a>語法

`parse_ipv4_mask(`*`Expr`*`, `*`PrefixMask`*`)`

## <a name="arguments"></a>引數

* *`Expr`*：將轉換為 long 的 IPv4 位址字串表示。 
* *`PrefixMask`*：從0到32的整數，代表納入考慮的最高有效位數目。

## <a name="returns"></a>傳回

如果轉換成功，結果將會是長數值。
如果轉換不成功，結果將會是 `null` 。
