---
title: 'acos ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 acos ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 486be5487071fa281765de410fe4e5be1ce62a38
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253112"
---
# <a name="acos"></a>acos()

傳回角度，其餘弦值 () 的反向運算中的指定數位 [`cos()`](cosfunction.md) 。

## <a name="syntax"></a>語法

`acos(`*X*`)`

## <a name="arguments"></a>引數

* *x*：範圍 [-1，1] 中的實數。

## <a name="returns"></a>傳回

* 的弧形余弦值 `x`
* `null` 如果 `x` <-1 或 `x` > 1