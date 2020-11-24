---
title: '子字串 ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中 ( # A1 的子字串。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 7b2f2dc18fe12f4bd07b638b6c3ca32d95a10618
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512038"
---
# <a name="substring"></a>substring()

從從某個索引開始到字串結尾的來源字串解壓縮子字串。

(選擇性) 您可以指定所要求子字串的長度。

```kusto
substring("abcdefg", 1, 2) == "bc"
```

## <a name="syntax"></a>語法

`substring(`*來源* `,`*startingIndex* [ `,` *長度*]`)`

## <a name="arguments"></a>引數

* *來源*：子字串將取自的來源字串。
* *startingIndex*：要求的子字串之以零為起始的字元位置。
* *length*：選擇性參數，可用來指定子字串中要求的字元數。 

**注意事項**

*startingIndex* 可以是負數，在此情況下，會從來源字串的結尾取出子字串。

## <a name="returns"></a>傳回

指定字串中的子字串。 子字串開始於 startingIndex (以零為基礎的) 字元位置，並延續到字串結尾或長度字元 (如果有指定)。

## <a name="examples"></a>範例

```kusto
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
substring("123456", -2, 2)    // 56
```