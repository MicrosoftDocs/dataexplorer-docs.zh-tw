---
title: isascii （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 isascii （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d8a060e4a332988fd966e0dec9ed07b3c76d0e3f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347284"
---
# <a name="isascii"></a>isascii()

`true`如果引數是有效的 ascii 字串，則傳回。
    
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