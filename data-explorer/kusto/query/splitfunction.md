---
title: split() - Azure 資料總管 | Microsoft Docs
description: 本文說明 API 資料總管中的 split()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
adobe-target: true
ms.openlocfilehash: ed58a1d249a74c02445968e81930e303aeff0d56
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2021
ms.locfileid: "100360059"
---
# <a name="split"></a>split()

根據指定的分隔符號分割指定字串，並傳回具有所包含子字串的字串陣列。

(選擇性) 可在特定子字串存在時將它傳回。

```kusto
split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]
```

## <a name="syntax"></a>語法

`split(`*source*`,` *delimiter* [`,` *requestedIndex*]`)`

## <a name="arguments"></a>引數

* *來源*：將根據指定分隔符號分割的來源字串。
* ︰: The  that will be used in order to split the source string.
* requestedIndex︰以零為基礎的選擇性索引 `int`。 如果提供，當要求的子字串存在時，傳回的字串陣列將會包含該子字串。 

## <a name="returns"></a>傳回

包含指定來源字串之子字串並以指定分隔符號加以分隔的字串陣列。

## <a name="examples"></a>範例

```kusto
print
    split("aa_bb", "_"),           // ["aa","bb"]
    split("aaa_bbb_ccc", "_", 1),  // ["bbb"]
    split("", "_"),                // [""]
    split("a__b", "_"),            // ["a","","b"]
    split("aabbcc", "bb")          // ["aa","cc"]
```