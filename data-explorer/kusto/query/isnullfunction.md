---
title: 'isnull ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中的 isnull ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: afaff2c00ca9136e113639deed886d039d21fda9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241518"
---
# <a name="isnull"></a>isnull()

評估其唯一的引數，並傳回 `bool` 值，指出引數是否評估為 null 值。

```kusto
isnull(parse_json("")) == true
```

## <a name="syntax"></a>語法

`isnull(`*Expr*`)`

## <a name="returns"></a>傳回

True 或 false，取決於值是否為 null。

**備註**

* `string` 值不可為 null。 使用 [isempty](./isemptyfunction.md) 來判斷類型的值是否為 `string` 空白。

|x                |`isnull(x)`|
|-----------------|-----------|
|`""`             |`false`    |
|`"x"`            |`false`    |
|`parse_json("")`  |`true`     |
|`parse_json("[]")`|`false`    |
|`parse_json("{}")`|`false`    |

## <a name="example"></a>範例

```kusto
T | where isnull(PossiblyNull) | count
```