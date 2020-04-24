---
title: 是空() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 isnull()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e26dca661ceac1ad209358b24b3f8d497a5c3049
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513408"
---
# <a name="isnull"></a>isnull()

計算其唯一參數並返回一`bool`個值,指示參數是否計算為空值。

```kusto
isnull(parse_json("")) == true
```

**語法**

`isnull(`*Expr*`)`

**傳回**

true 或 false，取決於值是 null 或不是 null。

**注意事項**

* `string`值不能為空。 使用[為空](./isemptyfunction.md)以`string`確定類型 值是否為空。

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