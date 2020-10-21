---
title: 'isascii ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 isascii ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a17836597277b2cf5401f2caeaa60b44da731dd5
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251776"
---
# <a name="isascii"></a>isascii()

`true`如果引數為有效的 ascii 字串，則傳回。
    
```kusto
isascii("some string") == true
```

## <a name="syntax"></a>語法

`isascii(`[*值*]`)`

## <a name="returns"></a>傳回

指出引數是否為有效的 ascii 字串。

## <a name="example"></a>範例

```kusto
T
| where isascii(fieldName)
| count
```