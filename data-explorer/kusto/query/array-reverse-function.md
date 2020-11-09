---
title: 'array_reverse ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 array_reverse。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: slneimer
ms.service: data-explorer
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 327564a953c8f537a0f76727b4313749f61eec3e
ms.sourcegitcommit: 4b061374c5b175262d256e82e3ff4c0cbb779a7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2020
ms.locfileid: "94373999"
---
# <a name="array_reverse"></a>array_reverse ( # A1

反轉動態陣列中的元素順序。

## <a name="syntax"></a>Syntax

`array_reverse(`*array*`)`

## <a name="arguments"></a>引數

*array* ：要反轉的輸入陣列。

## <a name="returns"></a>傳回

陣列，其中包含與輸入陣列完全相同的元素，但順序相反。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_reverse(arr)
```

|結果|
|---|
|["example"，"a"，"a"，"this"]|
