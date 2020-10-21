---
title: '簽署 ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 sign ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: db6757e8c3ca871036cba1c21c1a20c3486b9249
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250001"
---
# <a name="sign"></a>sign()

數值運算式的正負號

## <a name="syntax"></a>語法

`sign(`*X*`)`

## <a name="arguments"></a>引數

* *x*：實數。

## <a name="returns"></a>傳回

* 正 (+ 1) ，零 (0) 或負 (-1) 指定運算式的正負號。 

## <a name="examples"></a>範例

```kusto
print s1 = sign(-42), s2 = sign(0), s3 = sign(11.2)

```

|s1|s2|s3|
|---|---|---|
|-1|0|1|