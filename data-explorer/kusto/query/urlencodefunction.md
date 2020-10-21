---
title: 'url_encode ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 url_encode。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: a8c8e874fa4f6a1cb8c8731400e794e1359a4719
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92240983"
---
# <a name="url_encode"></a>url_encode()

函數會將輸入 URL 的字元轉換成可透過網際網路傳輸的格式。 

您可以在 [這裡](https://en.wikipedia.org/wiki/Percent-encoding)找到有關 URL 編碼和解碼的詳細資訊。
不同于 [url_encode_component](./urlencodecomponentfunction.md) ，方法是將空格編碼為 ' + '，而不是 ' 20% ' (請參閱 [此處](https://en.wikipedia.org/wiki/Percent-encoding)) 的 application/x-www 表單 urlencoded。

## <a name="syntax"></a>語法

`url_encode(`*Url*`)`

## <a name="arguments"></a>引數

* *url*：輸入 url (字串) 。  

## <a name="returns"></a>傳回

URL (字串) 轉換成可透過網際網路傳輸的格式。

## <a name="examples"></a>範例

```kusto
let url = @'https://www.bing.com/hello word';
print original = url, encoded = url_encode(url)
```

|原始|編碼|
|---|---|
|https://www.bing.com/hello 單字|HTTPs %3 a %2 f %2 f www. i .com% 2fhello + word|


 