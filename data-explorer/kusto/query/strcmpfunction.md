---
title: strcmp() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 strcmp()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 62ebcbfa71ebf8a29f3a8a1559feb91f96e0a2bf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506897"
---
# <a name="strcmp"></a>strcmp()

比較兩個字串。

函數開始比較每個字串的第一個字元。 如果它們彼此相等,則繼續使用以下對,直到字元不同或達到較短字串的末尾。

**語法**

`strcmp(`*字串1* `,` *字串2*`)` 

**引數**

* *字串1*:第一個輸入字串進行比較。 
* *string2*:第二個輸入字串進行比較。

**傳回**

傳回一個積分值,指示字串之間的關係:
* *<0* - 第一個不符合的字元在 string1 中的值低於 string2
* *0* - 兩個字串的內容相等
* *>0* - 第一個不符合的字元在字串1中的值大於字串2

**範例**

```
datatable(string1:string, string2:string)
["ABC","ABC",
"abc","ABC",
"ABC","abc",
"abcde","abc"]
| extend result = strcmp(string1,string2)
```

|string1|string2|result|
|---|---|---|
|ABC|ABC|0|
|abc|ABC|1|
|ABC|abc|-1|
|阿布德|abc|1|