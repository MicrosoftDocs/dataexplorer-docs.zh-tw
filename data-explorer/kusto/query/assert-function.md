---
title: assert （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 assert （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: a477a8fd8e05bd6420f06c28f71f72431a343a31
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349460"
---
# <a name="assert"></a>assert()

檢查條件。 如果條件為 false，則會輸出錯誤訊息，而且查詢會失敗。

## <a name="syntax"></a>語法

`assert(`*條件* `, `*訊息*`)`

## <a name="arguments"></a>引數

* *條件*：要評估的條件運算式。 如果條件為 `false` ，則會使用指定的訊息來報告錯誤。 如果條件為 `true` ，則會傳回 `true` 做為評估結果。 在查詢分析階段期間，必須將條件評估為常數。
* *message*：判斷提示評估為時所使用的訊息 `false` 。 *訊息*必須是字串常值。


## <a name="returns"></a>傳回

* `true`-如果條件為`true`
* 如果條件評估為，則引發語意錯誤 `false` 。

**備註**

* `condition`在查詢分析階段，必須評估為常數。 換句話說，它可以從參考常數的其他運算式來加以構造，而且無法系結至資料列內容。

## <a name="examples"></a>範例

下列查詢會定義一個函式，該函式會 `checkLength()` 檢查輸入字串長度，並使用 `assert` 來驗證輸入長度參數（檢查其是否大於零）。

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


以有效 `len` 輸入執行的範例：

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
