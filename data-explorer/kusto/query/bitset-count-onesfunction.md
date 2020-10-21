---
title: 'bitset_count_ones ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 bitset_count_ones。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: fb14ffa5807a61ade913631c7d6da6b23edc26d9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245327"
---
# <a name="bitset_count_ones"></a>bitset_count_ones()

傳回數位的二進位標記法中的設定位數目。

```kusto
bitset_count_ones(42)
```

## <a name="syntax"></a>語法

`bitset_count_ones(`*num1*' ') '

## <a name="arguments"></a>引數

* *num1*： long 或 integer 數位。

## <a name="returns"></a>傳回

傳回數位的二進位標記法中的設定位數目。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// 42 = 32+8+2 : b'00101010' == 3 bits set
print ones = bitset_count_ones(42) 
```

|的|
|---|
|3|
