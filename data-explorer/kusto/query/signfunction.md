---
title: sign （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 sign （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8d6761cc2ffa9a8c28151c00720bbd1340a56875
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351075"
---
# <a name="sign"></a>sign()

數值運算式的正負號

## <a name="syntax"></a>語法

`sign(`*x*`)`

## <a name="arguments"></a>引數

* *x*：實數。

## <a name="returns"></a>傳回

* 指定運算式的正（+ 1）、零（0）或負（-1）符號。 

## <a name="examples"></a>範例

```kusto
print s1 = sign(-42), s2 = sign(0), s3 = sign(11.2)

```

|s1|s2|s3|
|---|---|---|
|-1|0|1|