---
title: '分割 ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的分割 ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8ce8af3f9d56e4b5c3d388010b2760906a8e3dc4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242426"
---
# <a name="split"></a>split()

根據指定的分隔符號分割指定的字串，並傳回包含子字串的字串陣列。

(選擇性) 可在特定子字串存在時將它傳回。

```kusto
split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]
```

## <a name="syntax"></a>語法

`split(`*來源* `,`*分隔符號*[ `,` *requestedIndex*]`)`

## <a name="arguments"></a>引數

* *來源*：將根據指定的分隔符號進行分割的來源字串。
* ︰**: The  that will be used in order to split the source string.
* requestedIndex︰** 以零為基礎的選擇性索引 `int`。 如果提供，當要求的子字串存在時，傳回的字串陣列將會包含該子字串。 

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