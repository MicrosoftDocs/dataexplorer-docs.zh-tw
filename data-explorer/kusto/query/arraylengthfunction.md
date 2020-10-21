---
title: 'array_length ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 array_length。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3777194270e91d75d68c3af544e18b62358f709e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246771"
---
# <a name="array_length"></a>array_length()

計算動態陣列中的元素數目。

## <a name="syntax"></a>語法

`array_length(`*array*`)`

## <a name="arguments"></a>引數

* *陣列*： `dynamic` 值。

## <a name="returns"></a>傳回

array** 中的項目數，如果 array** 不是陣列，則為 `null`。

## <a name="examples"></a>範例

```kusto
print array_length(parse_json('[1, 2, 3, "four"]')) == 4

print array_length(parse_json('[8]')) == 1

print array_length(parse_json('[{}]')) == 1

print array_length(parse_json('[]')) == 0

print array_length(parse_json('{}')) == null

print array_length(parse_json('21')) == null
```