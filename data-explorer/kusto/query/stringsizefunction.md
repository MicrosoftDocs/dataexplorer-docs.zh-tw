---
title: 'string_size ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 string_size。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ea06e7e41ebe48e09839109745f3fe7ba5a96e7a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251463"
---
# <a name="string_size"></a>string_size()

傳回輸入字串的大小（以位元組為單位）。

## <a name="syntax"></a>語法

`string_size(`*源*`)`

## <a name="arguments"></a>引數

* *來源*：將針對字串大小測量的來源字串。

## <a name="returns"></a>傳回

傳回輸入字串的長度（以位元組為單位）。

## <a name="examples"></a>範例

```kusto
print size = string_size("hello")
```

|大小|
|---|
|5|

```kusto
print size = string_size("⒦⒰⒮⒯⒪")
```

|大小|
|---|
|15|