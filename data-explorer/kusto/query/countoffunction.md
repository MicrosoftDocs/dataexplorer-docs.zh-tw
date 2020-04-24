---
title: 計數() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 countof()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1d932fbcea9b38849e7d7de09230c9a5aa9fa8e4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516893"
---
# <a name="countof"></a>countof()

計算子字串在字串中的出現次數。 純文字字串的相符項目可能會重疊；regex 的相符項目則不會。

```kusto
countof("The cat sat on the mat", "at") == 3
countof("The cat sat on the mat", @"\b.at\b", "regex") == 3
```

**語法**

`countof(`*文字*`,`*搜尋*`,`[*類型*】`)`

**引數**

* *文字*:字串。
* *搜尋*: 要符合*文字*內部的純字串或[正規表示式](./re2.md)。
* *型態*`"normal"|"regex"`:`normal`預設值 。 

**傳回**

搜尋字串可在容器中相符的次數。 純文字字串的相符項目可能會重疊；regex 的相符項目則不會。

**範例**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (而非 2！)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    