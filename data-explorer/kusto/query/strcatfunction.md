---
title: strcat （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 strcat （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1f26b4bf267a4387748fe4c4c26636579607de51
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350990"
---
# <a name="strcat"></a>strcat()

會串連1到64個引數。

* 如果引數不是字串類型，則會強制將它們轉換成字串。

## <a name="syntax"></a>語法

`strcat(`*argument1*， *Argument2*[， *argumentN*]`)`

## <a name="arguments"></a>引數

* *argument1* .。。*argumentN*：要串連的運算式。

## <a name="returns"></a>傳回

引數，串連成單一字串。

## <a name="examples"></a>範例
  
   ```kusto
print str = strcat("hello", " ", "world")
```

|字串|
|---|
|hello world|
