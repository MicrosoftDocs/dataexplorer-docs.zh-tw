---
title: 'log2 ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 log2 ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: c5bab8e2f4106ddafc085fa35da424db60616a46
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243246"
---
# <a name="log2"></a>log2()

`log2()` 傳回以2為底數的對數函數。  

## <a name="syntax"></a>語法

`log2(`*X*`)`

## <a name="arguments"></a>引數

* *x*：實數 > 0。

## <a name="returns"></a>傳回

* 對數是以底數2為底數的對數：指數函式的反向 (exp) （基底2）。
* `null` 如果引數為負數或 null，或無法轉換成 `real` 值。 

## <a name="see-also"></a>另請參閱

* 針對自然 (e) 對數，請參閱 [log ( # B3 ](log-function.md)。
* 針對常見的 (base-10) 對數，請參閱 [log10 ( # B3 ](log10-function.md)。