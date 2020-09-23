---
title: 'log2 ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 log2 ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: e04d71ad0e88d42f93f9f34576c4f5f6e752ddb6
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103260"
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