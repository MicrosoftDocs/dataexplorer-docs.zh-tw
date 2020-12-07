---
title: substring() - Azure 資料總管 | Microsoft Docs
description: 本文說明 Azure 資料總管中的 substring()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 7b2f2dc18fe12f4bd07b638b6c3ca32d95a10618
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512038"
---
# <a name="substring"></a>substring()

從來源字串 (從某個索引開始到字串結尾) 中擷取子字串。

(選擇性) 您可以指定所要求子字串的長度。

```kusto
substring("abcdefg", 1, 2) == "bc"
```

## <a name="syntax"></a>語法

`substring(`*source*`,` *startingIndex* [`,` *length*]`)`

## <a name="arguments"></a>引數

* *來源*：要從中擷取子字串的來源字串。
* *startingIndex*：所要求子字串以零為基底的起始字元位置。
* *length*:可用來指定子字串中所要求字元數目的選擇性參數。 

**注意事項**

*startingIndex* 可以是負數，在這種情況下，將會從來源字串的結尾擷取子字串。

## <a name="returns"></a>傳回

指定字串中的子字串。 子字串開始於 startingIndex (以零為基礎的) 字元位置，並延續到字串結尾或長度字元 (如果有指定)。

## <a name="examples"></a>範例

```kusto
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
substring("123456", -2, 2)    // 56
```