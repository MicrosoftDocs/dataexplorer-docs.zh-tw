---
title: 'strcmp ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 strcmp ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 5c1e4cb9a4ad77d0d48f52ca7e47fc6cea06cff4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243743"
---
# <a name="strcmp"></a>strcmp()

比較兩個字串。

函數會開始比較每個字串的第一個字元。 如果它們彼此相等，則會繼續進行下列配對，直到字元不同或到達較短字串的結尾為止。

## <a name="syntax"></a>語法

`strcmp(`*string1* `,`*string2*`)` 

## <a name="arguments"></a>引數

* *string1*：要比較的第一個輸入字串。 
* *string2*：比較的第二個輸入字串。

## <a name="returns"></a>傳回

傳回整數值，指出字串之間的關聯性：
* *<0* -不相符的第一個字元在 string1 中的值比 string2 中的值低
* *0* -兩個字串的內容相等
* *>0* -不相符的第一個字元在 string1 中具有比 string2 中更大的值

## <a name="examples"></a>範例

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
|abcde|abc|1|