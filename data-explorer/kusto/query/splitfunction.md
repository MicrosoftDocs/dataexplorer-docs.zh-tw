---
title: 分割() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的拆分()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6e3935c524056afd27eb0d5e2d80925e072c8fa7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507458"
---
# <a name="split"></a>split()

根據給定的分隔符拆分給定字串,並返回包含子字串的字串的字串陣列。

(選擇性) 可在特定子字串存在時將它傳回。

```kusto
split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]
```

**語法**

`split(`*來源*`,`*分隔符*`,` [*請求的索引*]`)`

**引數**

* *來源*:將根據給定分隔符拆分的原始碼字串。
* ︰**: The  that will be used in order to split the source string.
* requestedIndex︰** 以零為基礎的選擇性索引 `int`。 如果提供，當要求的子字串存在時，傳回的字串陣列將會包含該子字串。 

**傳回**

包含指定來源字串之子字串並以指定分隔符號加以分隔的字串陣列。

**範例**

```kusto
print
    split("aa_bb", "_"),           // ["aa","bb"]
    split("aaa_bbb_ccc", "_", 1),  // ["bbb"]
    split("", "_"),                // [""]
    split("a__b", "_"),            // ["a","","b"]
    split("aabbcc", "bb")          // ["aa","cc"]
```