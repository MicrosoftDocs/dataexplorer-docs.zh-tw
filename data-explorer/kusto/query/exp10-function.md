---
title: exp10 （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 exp10 （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2019
ms.openlocfilehash: 260fbc24bb5cb653cf6df9e976c2c78682eb9ab6
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348185"
---
# <a name="exp10"></a>exp10()

X 的以10為底數的指數函式，其為乘冪 x： 10 ^ x 的10。  

## <a name="syntax"></a>語法

`exp10(`*x*`)`

## <a name="arguments"></a>引數

* *x*：指數的實數值。

## <a name="returns"></a>傳回

* X 的指數值。
* 若為自然（底數為10）對數，請參閱[log10 （）](log10-function.md)。
* 如需以 e 為底數的指數函式和以2為底數的對數，請參閱[exp （）](exp-function.md)、 [exp2 （）](exp2-function.md)