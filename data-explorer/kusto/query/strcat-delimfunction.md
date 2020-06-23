---
title: strcat_delim （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 strcat_delim （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f6a78a5abb92aa93fe8b1ae15ea8968f71bde07c
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264550"
---
# <a name="strcat_delim"></a>strcat_delim()

會串連2到64個引數，並以分隔符號形式提供作為第一個引數。

 * 如果引數不是字串類型，則會強制將它們轉換成字串。

**語法**

`strcat_delim(`*分隔符號*， *argument1*， *argument2*[， *argumentN*]`)`

**引數**

* *分隔符號*：字串運算式，將用來做為分隔符號。
* *argument1* .。。*argumentN*：要串連的運算式。

**傳回**

引數，串連成具有*分隔符號*的單一字串。

**範例**

```kusto
print st = strcat_delim('-', 1, '2', 'A', 1s)

```

|st|
|---|
|1-2-A-00:00:01|
