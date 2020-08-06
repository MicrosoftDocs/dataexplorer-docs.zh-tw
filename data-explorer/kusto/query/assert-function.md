---
title: 'assert ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中的 assert ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 050974af47b0f5cd0e041694ee5f680b8c321614
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803312"
---
# <a name="assert"></a>assert()

檢查條件。 如果條件為 false，則會輸出錯誤訊息，而且查詢會失敗。

## <a name="syntax"></a>語法

`assert(`*條件* `, `*訊息*`)`

## <a name="arguments"></a>引數

* *條件*：要評估的條件運算式。 如果條件為 `false` ，則會使用指定的訊息來報告錯誤。 如果條件為 `true` ，則會傳回 `true` 做為評估結果。 在查詢分析階段期間，必須將條件評估為常數。
* *message*：判斷提示評估為時所使用的訊息 `false` 。 *訊息*必須是字串常值。

> [!NOTE]
> `condition`在查詢分析階段，必須評估為常數。 換句話說，它可以從參考常數的其他運算式來加以構造，而且無法系結至資料列內容。

## <a name="returns"></a>傳回

* `true`-如果條件為`true`
* 如果條件評估為，則引發語意錯誤 `false` 。

## <a name="examples"></a>範例

下列查詢 `checkLength()` 會定義檢查輸入字串長度的函式，並使用 `assert` 來驗證輸入長度參數， (檢查其是否大於零) 。

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
