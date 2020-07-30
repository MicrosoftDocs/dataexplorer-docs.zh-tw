---
title: log （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 log （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 283cd1427dedb04b036d7cb23e650d8f0651a58c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347131"
---
# <a name="log"></a>log()

`log()`傳回自然對數函數。  

## <a name="syntax"></a>語法

`log(`*x*`)`

## <a name="arguments"></a>引數

* *x*：實數 > 0。

## <a name="returns"></a>傳回

* 自然對數是以 e 為底數的對數：自然指數函數（exp）的反向。
* `null`如果引數為負數或 null 或無法轉換為值，則為 `real` 。 

**另請參閱**

* 針對常見（以10為基底）的對數，請參閱[log10 （）](log10-function.md)。
* 若是以2為底數的對數，請參閱[log2 （）](log2-function.md)