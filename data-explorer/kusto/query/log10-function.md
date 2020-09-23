---
title: 'log10 ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 log10 ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 62813f5680e89c2bca0a6ec547fd7055ddc0e414
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103194"
---
# <a name="log10"></a>log10()

`log10()` 傳回 common (base-10) 對數函數。  

## <a name="syntax"></a>語法

`log10(`*X*`)`

## <a name="arguments"></a>引數

* *x*：實數 > 0。

## <a name="returns"></a>傳回

* 一般對數是以10為底數的對數：指數函式的反向 (exp) （以10為底數）。
* `null` 如果引數為負數或 null，或無法轉換成 `real` 值。 

## <a name="see-also"></a>另請參閱

* 針對自然 (e) 對數，請參閱 [log ( # B3 ](log-function.md)。
* 如需以2為底數的對數，請參閱 [log2 ( # B1 ](log2-function.md)