---
title: 'url_decode ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 url_decode。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b58fea5d367cf31b495b23a09bc0a0dcb6bb95c6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251854"
---
# <a name="url_decode"></a>url_decode()

函式會將編碼的 URL 轉換為一般 URL 標記法。 

您可以在 [這裡](https://en.wikipedia.org/wiki/Percent-encoding)找到有關 URL 解碼和編碼的詳細資訊。

## <a name="syntax"></a>語法

`url_decode(`*編碼的 url*`)`

## <a name="arguments"></a>引數

* *編碼的 url*：編碼的 url (字串) 。  

## <a name="returns"></a>傳回

一般標記法中) 的 URL (字串。

## <a name="examples"></a>範例

```kusto
let url = @'https%3a%2f%2fwww.bing.com%2f';
print original = url, decoded = url_decode(url)
```

|原始|解碼|
|---|---|
|HTTPs %3 a %2 f %2 f www. i .com% 2f|https://www.bing.com/|



 