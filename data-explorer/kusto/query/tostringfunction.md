---
title: 到字串() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 tostring()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 51aaf90b60653a648457dc00200168aec7fbefd9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505877"
---
# <a name="tostring"></a>tostring()

將輸入轉換為字串表示形式。

```kusto
tostring(123) == "123"
```

**語法**

`tostring(`*Expr*`)`

**引數**

* *Expr*: 將轉換為字串的運算式。 

**傳回**

如果*Expr*值是非空結果,則*Expr*的字串表示形式。
如果*Expr*值為空,則結果將為空字串。
 