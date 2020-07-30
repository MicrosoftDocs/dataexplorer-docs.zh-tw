---
title: asin （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 asin （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 360a936d136cd01760175cdf5b5da2718d0cfd91
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349494"
---
# <a name="asin"></a>asin()

傳回其正弦為指定數位的角度（的反運算 [`sin()`](sinfunction.md) ）。

## <a name="syntax"></a>語法

`asin(`*x*`)`

## <a name="arguments"></a>引數

* *x*：範圍 [-1，1] 中的實數。

## <a name="returns"></a>傳回

* 的反正弦值`x`
* `null`如果 `x` <-1 或 `x` > 1