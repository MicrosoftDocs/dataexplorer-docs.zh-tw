---
title: isnull （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 isnull （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d1bea6260ca86e6ca47be843a6acc4fb43a037b3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347165"
---
# <a name="isnull"></a>isnull()

評估其唯一引數，並傳回 `bool` 值，指出引數是否評估為 null 值。

```kusto
isnull(parse_json("")) == true
```

## <a name="syntax"></a>語法

`isnull(`*Expr*`)`

## <a name="returns"></a>傳回

True 或 false，視值是否為 null 而定。

**備註**

* `string`值不可以是 null。 請使用[isempty](./isemptyfunction.md)來判斷類型的值 `string` 是否為空的。

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