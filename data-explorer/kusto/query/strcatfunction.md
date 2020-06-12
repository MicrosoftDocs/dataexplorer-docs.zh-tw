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
ms.openlocfilehash: af25bb0407c9bc0c004c2f22e326ac034c6682cd
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717099"
---
# <a name="strcat"></a>strcat()

會串連1到64個引數。

* 如果引數不是字串類型，則會強制將它們轉換成字串。

**語法**

`strcat(`*argument1*， *Argument2*[， *argumentN*]`)`

**引數**

* *argument1* .。。*argumentN*：要串連的運算式。

**傳回**

引數，串連成單一字串。

**範例**
  
   ```kusto
print str = strcat("hello", " ", "world")
```

|字串|
|---|
|hello world|
