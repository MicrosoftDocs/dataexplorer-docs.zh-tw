---
title: 'isutf8 ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 isutf8 ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c9ad35964eb6d5a8c4a38b5ecdeb1eae43221d52
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247344"
---
# <a name="isutf8"></a>isutf8()

`true`如果引數為有效的 utf8 字串，則傳回。
    
```kusto
isutf8("some string") == true
```

## <a name="syntax"></a>語法

`isutf8(`[*值*]`)`

## <a name="returns"></a>傳回

指出引數是否為有效的 utf8 字串。

## <a name="example"></a>範例

```kusto
T
| where isutf8(fieldName)
| count
```