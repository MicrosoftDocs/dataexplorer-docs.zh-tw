---
title: 'asin ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 asin ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: e23ddda4bbc2e165adfd810e1dc27b5ffd14edf4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246746"
---
# <a name="asin"></a>asin()

傳回角度，其正弦值為指定的數位 () 的反向運算 [`sin()`](sinfunction.md) 。

## <a name="syntax"></a>語法

`asin(`*X*`)`

## <a name="arguments"></a>引數

* *x*：範圍 [-1，1] 中的實數。

## <a name="returns"></a>傳回

* 的弧線正弦值 `x`
* `null` 如果 `x` <-1 或 `x` > 1