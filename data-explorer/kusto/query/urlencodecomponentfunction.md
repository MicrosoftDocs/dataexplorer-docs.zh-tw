---
title: 'url_encode_component ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 url_encode_component。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 6740effd6a6117a2e63b5d03f09b38a723055f30
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241141"
---
# <a name="url_encode_component"></a>url_encode_component()

函數會將輸入 URL 的字元轉換成可透過網際網路傳輸的格式。 

您可以在 [這裡](https://en.wikipedia.org/wiki/Percent-encoding)找到有關 URL 編碼和解碼的詳細資訊。
不同于 [url_encode](./urlencodefunction.md) ，方法是將空格編碼為 ' 20% '，而不是 ' + '。

## <a name="syntax"></a>語法

`url_encode_component(`*Url*`)`

## <a name="arguments"></a>引數

* *url*：輸入 url (字串) 。  

## <a name="returns"></a>傳回

URL (字串) 轉換成可透過網際網路傳輸的格式。

## <a name="examples"></a>範例

```kusto
let url = @'https://www.bing.com/hello word/';
print original = url, encoded = url_encode_component(url)
```

|原始|編碼|
|---|---|
|https://www.bing.com/hello 單字|HTTPs %3 a %2 f %2 f www. i .com% 2fhello% 20word|


 