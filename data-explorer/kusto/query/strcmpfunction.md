---
title: strcmp （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 strcmp （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 11951195d95d956f70d4bfce32d22a9f9c73dc3d
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350922"
---
# <a name="strcmp"></a>strcmp()

比較兩個字串。

函式會開始比較每個字串的第一個字元。 如果兩者相等，則會以下列配對繼續進行，直到字元不同或到達較短的字串結尾為止。

## <a name="syntax"></a>語法

`strcmp(`*string1* `,`*string2*`)` 

## <a name="arguments"></a>引數

* *string1*：要比較的第一個輸入字串。 
* *string2*：用於比較的第二個輸入字串。

## <a name="returns"></a>傳回

傳回整數值，表示字串之間的關聯性：
* *<0* -不符合的第一個字元在 string1 中的值比 string2 中的值低
* *0* -兩個字串的內容相等
* *>0* -不符合的第一個字元，在 string1 中的值大於 string2

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