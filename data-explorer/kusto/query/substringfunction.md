---
title: substring （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的子字串（）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b0e83e8d0baf33e5c11cb8b7ecafa607a08fe32b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350854"
---
# <a name="substring"></a>substring()

從從某個索引開始到字串結尾的來源字串，將子字串解壓縮。

(選擇性) 您可以指定所要求子字串的長度。

```kusto
substring("abcdefg", 1, 2) == "bc"
```

## <a name="syntax"></a>語法

`substring(`*來源* `,`*startingIndex* [ `,` *長度*]`)`

## <a name="arguments"></a>引數

* *來源*：將從中取得子字串的來源字串。
* *startingIndex*：所要求子字串之以零為起始的起始字元位置。
* *length*：選擇性參數，可用來指定子字串中所要求的字元數。 

**備註**

*startingIndex*可以是負數，在此情況下，將會從來源字串的結尾抓取子字串。

## <a name="returns"></a>傳回

指定字串中的子字串。 子字串開始於 startingIndex (以零為基礎的) 字元位置，並延續到字串結尾或長度字元 (如果有指定)。

## <a name="examples"></a>範例

```kusto
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
substring("123456", -2, 2)    // 56
```