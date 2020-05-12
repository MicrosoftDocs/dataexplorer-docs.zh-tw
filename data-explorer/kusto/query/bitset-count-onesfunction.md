---
title: bitset_count_ones （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 bitset_count_ones （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: f8abb1683a2f15f012e9a9271681688c19901af0
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227585"
---
# <a name="bitset_count_ones"></a>bitset_count_ones()

傳回數位的二進位標記法中的集合位數目。

```kusto
bitset_count_ones(42)
```

**語法**

`bitset_count_ones(`*num1*' '） '

**引數**

* *num1*： long 或 integer 數位。

**傳回**

傳回數位的二進位標記法中的集合位數目。

**範例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// 42 = 32+8+2 : b'00101010' == 3 bits set
print ones = bitset_count_ones(42) 
```

|漏洞|
|---|
|3|
