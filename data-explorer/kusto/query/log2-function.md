---
title: log2 （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 log2 （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: a404dfba70f3a624acc08e70ee4b24935d1d7135
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347097"
---
# <a name="log2"></a>log2()

`log2()`傳回以2為底數的對數函數。  

## <a name="syntax"></a>語法

`log2(`*x*`)`

## <a name="arguments"></a>引數

* *x*：實數 > 0。

## <a name="returns"></a>傳回

* 對數是以2為底數的對數：指數函數（exp）的反向（以基底2為底數）。
* `null`如果引數為負數或 null 或無法轉換為值，則為 `real` 。 

**另請參閱**

* 如需自然（底數為 e）的對數，請參閱[log （）](log-function.md)。
* 針對常見（以10為基底）的對數，請參閱[log10 （）](log10-function.md)。