---
title: bitset_count_ones() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的bitset_count_ones()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: c23e95ee9f00a0ca173d68d3591ad604b31dfac2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517386"
---
# <a name="bitset_count_ones"></a>bitset_count_ones()

返回數位二進位表示法中的設置位數。

```kusto
bitset_count_ones(42)
```

**語法**

`bitset_count_ones(`*num1*'')'

**引數**

* *num1:* 長數或整數。

**傳回**

返回數位二進位表示法中的設置位數。

**範例**

```kusto
// 42 = 32+8+2 : b'00101010' == 3 bits set
print ones = bitset_count_ones(42) 
```

|的|
|---|
|3|