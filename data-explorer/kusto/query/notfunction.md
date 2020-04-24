---
title: 非() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 not()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 539e409a9e922afc390b097c863146b7fc30d7b3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512031"
---
# <a name="not"></a>not()

反轉其`bool`參數的值。

```kusto
not(false) == true
```

**語法**

`not(`*expr*`)`

**引數**

* *expr*`bool`: 要反轉的運算式。

**傳回**

返回其`bool`參數的反向邏輯值。