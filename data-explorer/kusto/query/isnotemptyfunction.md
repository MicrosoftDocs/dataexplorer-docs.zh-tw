---
title: 'isnotempty ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中的 isnotempty ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 42699b1d4a6f225213b2a2d55520cb019a983121
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253228"
---
# <a name="isnotempty"></a>isnotempty()

`true`如果引數不是空字串，則傳回，而且不是 null。

```kusto
isnotempty("") == false
```

## <a name="syntax"></a>語法

`isnotempty(`[*值*]`)`

`notempty(`[*值*] `)` --別名 `isnotempty`

## <a name="example"></a>範例

```kusto
T
| where isnotempty(fieldName)
| count
```
