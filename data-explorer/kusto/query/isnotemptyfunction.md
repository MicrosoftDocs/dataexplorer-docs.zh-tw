---
title: 'isnotempty ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中的 isnotempty ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9f7811c2668bd0bb2d6e3711800ee5d9a450b9ec
ms.sourcegitcommit: 487485c87706183a9824f695e5b56b0bc5ade048
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89056229"
---
# <a name="isnotempty"></a>isnotempty()

`true`如果引數不是空字串，則傳回，而且不是 null。

```kusto
isnotempty("") == false
```

## <a name="syntax"></a>Syntax

`isnotempty(`[*值*]`)`

`notempty(`[*值*] `)` --別名 `isnotempty`

## <a name="example"></a>範例

```kusto
T
| where isnotempty(fieldName)
| count
```
