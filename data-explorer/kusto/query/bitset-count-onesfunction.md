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
ms.openlocfilehash: b8ebef923d1cc67c118317680e1ec414900a469e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348950"
---
# <a name="bitset_count_ones"></a>bitset_count_ones()

傳回數位的二進位標記法中的集合位數目。

```kusto
bitset_count_ones(42)
```

## <a name="syntax"></a>語法

`bitset_count_ones(`*num1*' '） '

## <a name="arguments"></a>引數

* *num1*： long 或 integer 數位。

## <a name="returns"></a>傳回

傳回數位的二進位標記法中的集合位數目。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// 42 = 32+8+2 : b'00101010' == 3 bits set
print ones = bitset_count_ones(42) 
```

|漏洞|
|---|
|3|
