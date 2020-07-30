---
title: isempty （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 isempty （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ac2bf5d5ea55172cbdb07bf90704ae5ad497e925
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347267"
---
# <a name="isempty"></a>isempty()

`true`如果引數為空字串或為 null，則傳回。
    
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
|parsejson （" {} "）|false

## <a name="example"></a>範例

```kusto
T
| where isempty(fieldName)
| count
```