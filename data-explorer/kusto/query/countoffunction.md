---
title: 'countof ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 countof ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d4d4ac00ad05d62f95901c24ea91af8cace0322e
ms.sourcegitcommit: bc09599c282b20b5be8f056c85188c35b66a52e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88610530"
---
# <a name="countof"></a>countof()

計算子字串在字串中的出現次數。 純文字字串的相符項目可能會重疊；regex 的相符項目則不會。

```kusto
countof("The cat sat on the mat", "at") == 3
countof("The cat sat on the mat", @"\b.at\b", "regex") == 3
```

## <a name="syntax"></a>語法

`countof(`*文字* `,`*搜尋*[ `,` *kind*]`)`

## <a name="arguments"></a>引數

* *text*：字串。
* *搜尋*：要比對*文字*內的純字串或[正則運算式](./re2.md)。
* *種類*： `"normal"|"regex"` 預設值 `normal` 。 

## <a name="returns"></a>傳回

搜尋字串可在容器中相符的次數。 純文字字串的相符項目可能會重疊；regex 的相符項目則不會。

## <a name="examples"></a>範例

|函式呼叫|結果|
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (而非 2！)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    