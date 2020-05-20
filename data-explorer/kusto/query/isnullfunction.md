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
ms.openlocfilehash: 4c57c7aba2bff2dfaecfa72b20ab76cc84ed17d6
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550583"
---
# <a name="isnull"></a>isnull()

評估其唯一引數，並傳回 `bool` 值，指出引數是否評估為 null 值。

```kusto
isnull(parse_json("")) == true
```

**語法**

`isnull(`*Expr*`)`

**傳回**

True 或 false，視值是否為 null 而定。

**注意事項**

* `string`值不可以是 null。 請使用[isempty](./isemptyfunction.md)來判斷類型的值 `string` 是否為空的。

|x                |`isnull(x)`|
|-----------------|-----------|
|`""`             |`false`    |
|`"x"`            |`false`    |
|`parse_json("")`  |`true`     |
|`parse_json("[]")`|`false`    |
|`parse_json("{}")`|`false`    |

**範例**

```kusto
T | where isnull(PossiblyNull) | count
```