---
title: array_length() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的array_length()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0bb1daeba6d24f8bd7326fcd0b8c17f06003e30b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518593"
---
# <a name="array_length"></a>array_length()

計算動態陣列中的元素數。

**語法**

`array_length(`*陣列*`)`

**引數**

* *陣列*`dynamic`: 值。

**傳回**

array** 中的項目數，如果 array** 不是陣列，則為 `null`。

**範例**

```kusto
print array_length(parse_json('[1, 2, 3, "four"]')) == 4

print array_length(parse_json('[8]')) == 1

print array_length(parse_json('[{}]')) == 1

print array_length(parse_json('[]')) == 0

print array_length(parse_json('{}')) == null

print array_length(parse_json('21')) == null
```