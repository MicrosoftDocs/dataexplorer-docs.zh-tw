---
title: 'assert ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中的 assert ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 442fbec2742a4d1edc7a9ad03a81db27e6d18574
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252781"
---
# <a name="assert"></a>assert()

檢查條件。 如果條件為 false，則會輸出錯誤訊息，並導致查詢失敗。

## <a name="syntax"></a>語法

`assert(`*條件* `, `*訊息*`)`

## <a name="arguments"></a>引數

* *條件*：要評估的條件運算式。 如果條件為 `false` ，則會使用指定的訊息來報告錯誤。 如果條件為 `true` ，則會傳回 `true` 做為評估結果。 在查詢分析階段，條件必須評估為常數。
* *message*：當判斷提示評估為時，所使用的訊息 `false` 。 *訊息*必須是字串常值。

> [!NOTE]
> `condition` 在查詢分析階段，必須評估為常數。 換句話說，它可以從參考常數的其他運算式來建立，且不能系結至資料列內容。

## <a name="returns"></a>傳回

* `true` -如果條件為 `true`
* 如果條件評估為，則會引發語意錯誤 `false` 。

## <a name="examples"></a>範例

下列查詢 `checkLength()` 會定義檢查輸入字串長度的函式，並使用 `assert` 來驗證輸入長度參數， (檢查它是否大於零) 。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let checkLength = (len:long, s:string)
{
    assert(len > 0, "Length must be greater than zero") and 
    strlen(s) > len
};
datatable(input:string)
[
    '123',
    '4567'
]
| where checkLength(len=long(-1), input)
```

執行此查詢會產生錯誤：  
`assert() has failed with message: 'Length must be greater than zero'`


使用有效 `len` 輸入執行的範例：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let checkLength = (len:long, s:string)
{
    assert(len > 0, "Length must be greater than zero") and strlen(s) > len
};
datatable(input:string)
[
    '123',
    '4567'
]
| where checkLength(len=3, input)
```

|input|
|---|
|4567|
