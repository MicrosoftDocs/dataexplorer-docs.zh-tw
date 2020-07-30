---
title: isutf8 （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 isutf8 （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 952ea030d351a9e23fe26bbd7f27a96d182a89e3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347148"
---
# <a name="isutf8"></a>isutf8()

`true`如果引數是有效的 utf8 字串，則傳回。
    
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