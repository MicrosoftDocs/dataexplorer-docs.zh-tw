---
title: url_encode （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 url_encode （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 8ccc93286073003bdaf8324611888d32f60910fb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350565"
---
# <a name="url_encode"></a>url_encode()

函式會將輸入 URL 的字元轉換成可透過網際網路傳輸的格式。 

您可以在[這裡](https://en.wikipedia.org/wiki/Percent-encoding)找到有關 URL 編碼和解碼的詳細資訊。
將空格編碼為 ' + ' 而不是 ' 20% '，不同于[url_encode_component](./urlencodecomponentfunction.md) （請參閱[這裡](https://en.wikipedia.org/wiki/Percent-encoding)的 application/x-www-urlencoded）。

## <a name="syntax"></a>語法

`url_encode(`*連結*`)`

## <a name="arguments"></a>引數

* *url*：輸入 url （字串）。  

## <a name="returns"></a>傳回

URL （字串）已轉換成可透過網際網路傳輸的格式。

## <a name="examples"></a>範例

```kusto
let url = @'https://www.bing.com/hello word';
print original = url, encoded = url_encode(url)
```

|原始|等效|
|---|---|
|https://www.bing.com/hello斷|HTTPs %3 a %2 f %2 f www. bing .com% 2fhello + word|


 