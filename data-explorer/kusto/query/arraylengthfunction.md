---
title: array_length （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 array_length （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 14203e3078b7fe30222ea26320ed1391000d5c05
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349545"
---
# <a name="array_length"></a>array_length()

計算動態陣列中的元素數目。

## <a name="syntax"></a>語法

`array_length(`*array*`)`

## <a name="arguments"></a>引數

* *array*： `dynamic` 值。

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