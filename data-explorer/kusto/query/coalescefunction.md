---
title: 合併() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的合併()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1ee2cd24f36007914fdc326e2863da148aec4406
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517131"
---
# <a name="coalesce"></a>coalesce()

計算運算式清單並返回第一個非空(或字串的非空)運算式。

```kusto
coalesce(tolong("not a number"), tolong("42"), 33) == 42
```

**語法**

`coalesce(`*expr_1*`, ` expr_1expr_2`,` ...)* *

**引數**

* *expr_i*: 要計算的標量運算式。
- 所有參數必須具有相同的類型。
- 最多支援 64 個參數。


**傳回**

第一個值不為空(或字串表達式為非空)的第一*個expr_i*的值。

**範例**

```kusto
print result=coalesce(tolong("not a number"), tolong("42"), 33)
```

|result|
|---|
|42|