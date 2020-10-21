---
title: 'isempty ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 isempty ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2944e90f14f38e2f136f5815a95584edc50d8235
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251725"
---
# <a name="isempty"></a>isempty()

`true`如果引數為空字串或為 null，則會傳回。
    
```kusto
isempty("") == true
```

## <a name="syntax"></a>語法

`isempty(`[*值*]`)`

## <a name="returns"></a>傳回

指出引數為空字串還是 null。

|x|isempty(x)
|---|---
| "" | true
|"x" | false
|parsejson("")|true
|parsejson("[]")|false
|parsejson ( " {} " ) |false

## <a name="example"></a>範例

```kusto
T
| where isempty(fieldName)
| count
```