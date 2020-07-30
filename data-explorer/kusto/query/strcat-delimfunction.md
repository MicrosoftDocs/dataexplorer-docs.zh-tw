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
ms.openlocfilehash: 2568196dc20042e95521ed0818bd625f3394599b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342558"
---
# <a name="strcat_delim"></a>strcat_delim()

會串連2到64個引數，並以分隔符號形式提供作為第一個引數。

 * 如果引數不是字串類型，則會強制將它們轉換成字串。

## <a name="syntax"></a>語法

`strcat_delim(`*分隔符號*， *argument1*， *argument2*[， *argumentN*]`)`

## <a name="arguments"></a>引數

* *分隔符號*：字串運算式，將用來做為分隔符號。
* *argument1* .。。*argumentN*：要串連的運算式。

## <a name="returns"></a>傳回

引數，串連成具有*分隔符號*的單一字串。

## <a name="examples"></a>範例

```kusto
print st = strcat_delim('-', 1, '2', 'A', 1s)

```

|st|
|---|
|1-2-A-00:00:01|
