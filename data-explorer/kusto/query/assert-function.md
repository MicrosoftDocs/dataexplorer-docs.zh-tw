---
title: 斷言() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 assert()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 97f01df7bc85171eefabeb2ed835109f0faef81e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518406"
---
# <a name="assert"></a>assert()

檢查條件。 如果條件為 false,則輸出錯誤消息並失敗查詢。

**語法**

`assert(`*條件*`, `*訊息*`)`

**引數**

* *條件*:要計算的條件表達式。 如果條件為`false`,則指定的消息用於報告錯誤。 如果條件為`true`,它將`true`作為評估結果返回。 在查詢分析階段,必須將條件計算為常量。
* *消息*: 如果斷言`false`計算到 ,則使用的消息。 *消息*必須是字串文字。


**傳回**

* `true`- 如果條件為`true`
* 如果條件計算到`false`,則引發語義錯誤。

**注意事項**

* `condition`必須在查詢分析階段計算為常量。 換句話說,它可以從引用常量的其他表達式構造,並且不能綁定到行上下文。

**範例**

以下查詢定義一個函數`checkLength()`,用於檢查輸入字串長度,並用於`assert`驗證輸入長度參數(檢查其大於零)。

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

執行此查詢會產生錯誤:  
`assert() has failed with message: 'Length must be greater than zero'`


使用有效輸入執行的範例`len`:

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