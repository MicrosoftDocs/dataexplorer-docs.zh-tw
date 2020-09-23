---
title: '記錄 ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的記錄 ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: e81266bf43da93d2b36f0be5846e5d74f3157c7f
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103221"
---
# <a name="log"></a>log()

`log()` 傳回自然對數函數。  

## <a name="syntax"></a>語法

`log(`*X*`)`

## <a name="arguments"></a>引數

* *x*：實數 > 0。

## <a name="returns"></a>傳回

* 自然對數是以 e 為底數的對數：自然指數函數的反向 (exp) 。
* `null` 如果引數為負數或 null，或無法轉換成 `real` 值。 

## <a name="see-also"></a>另請參閱

* 針對常見的 (base-10) 對數，請參閱 [log10 ( # B3 ](log10-function.md)。
* 如需以2為底數的對數，請參閱 [log2 ( # B1 ](log2-function.md)