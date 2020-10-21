---
title: 'strcat ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中的 strcat ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 55a52ff4aece3fa1a3197db0fb47984217292136
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251516"
---
# <a name="strcat"></a>strcat()

串連1和64引數。

* 如果引數不是字串類型，則會強制將這些引數轉換成字串。

## <a name="syntax"></a>語法

`strcat(`*引數 1*， *引數 2*[， *argumentN*]`)`

## <a name="arguments"></a>引數

* *引數 1* .。。 *argumentN*：要串連的運算式。

## <a name="returns"></a>傳回

引數，串連成單一字串。

## <a name="examples"></a>範例
  
   ```kusto
print str = strcat("hello", " ", "world")
```

|字串|
|---|
|hello world|
