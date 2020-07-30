---
title: strrep （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 strrep （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 797684aa70cb8205463cc41558afec82449c433d
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350871"
---
# <a name="strrep"></a>strrep()

重複指定的[字串](./scalar-data-types/string.md)所提供的時間量。

* 如果第一個或第三個引數不是字串類型，則會強制將它轉換成字串。

## <a name="syntax"></a>語法

`strrep(`*值*、*乘數*、[*分隔符號*]`)`

## <a name="arguments"></a>引數

* *值*：輸入運算式
* *乘數*：正整數值（從1到1024）
* *分隔符號*：選擇性的字串運算式（預設值：空字串）

## <a name="returns"></a>傳回

針對指定次數重複的值，與*分隔符號*串連。

如果*乘數*大於允許的最大值（1024），則輸入字串會重複1024次。
 
## <a name="example"></a>範例

```kusto
print from_str = strrep('ABC', 2), from_int = strrep(123,3,'.'), from_time = strrep(3s,2,' ')
```

|from_str|from_int|from_time|
|---|---|---|
|ABCABC|123.123.123|00:00:03 00:00:03|