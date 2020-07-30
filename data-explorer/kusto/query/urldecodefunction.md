---
title: url_decode （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 url_decode （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0019e318b90f9626d9e55a593f19526cdc7cc9c7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350582"
---
# <a name="url_decode"></a>url_decode()

函式會將編碼的 URL 轉換成一般 URL 標記法。 

您可以在[這裡](https://en.wikipedia.org/wiki/Percent-encoding)找到有關 URL 解碼和編碼的詳細資訊。

## <a name="syntax"></a>語法

`url_decode(`*編碼的 url*`)`

## <a name="arguments"></a>引數

* *編碼的 url*：編碼的 url （字串）。  

## <a name="returns"></a>傳回

一般標記法中的 URL （字串）。

## <a name="examples"></a>範例

```kusto
let url = @'https%3a%2f%2fwww.bing.com%2f';
print original = url, decoded = url_decode(url)
```

|原始|後|
|---|---|
|HTTPs %3 a %2 f %2 f www. bing .com% 2f|https://www.bing.com/|



 