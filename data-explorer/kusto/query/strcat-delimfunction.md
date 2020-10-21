---
title: 'strcat_delim ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 strcat_delim。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: de34f4a2f6efb3af84b61f53da8d58cb60dea7ae
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243694"
---
# <a name="strcat_delim"></a>strcat_delim()

使用分隔符號（以第一個引數提供）來串連2和64引數。

 * 如果引數不是字串類型，則會強制將這些引數轉換成字串。

## <a name="syntax"></a>語法

`strcat_delim(`*分隔符號*， *引數 1*， *引數 2*[， *argumentN*]`)`

## <a name="arguments"></a>引數

* *分隔符號*：將用作分隔符號的字串運算式。
* *引數 1* .。。 *argumentN*：要串連的運算式。

## <a name="returns"></a>傳回

引數，串連成具有 *分隔符號*的單一字串。

## <a name="examples"></a>範例

```kusto
print st = strcat_delim('-', 1, '2', 'A', 1s)

```

|st|
|---|
|1-2-A-00:00:01|
