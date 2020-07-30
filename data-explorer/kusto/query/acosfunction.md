---
title: acos （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 acos （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d1daf36e85eec4c8543ba14be153a9d6069e1cd1
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349851"
---
# <a name="acos"></a>acos()

傳回其餘弦為指定數位的角度（的反運算 [`cos()`](cosfunction.md) ）。

## <a name="syntax"></a>語法

`acos(`*x*`)`

## <a name="arguments"></a>引數

* *x*：範圍 [-1，1] 中的實數。

## <a name="returns"></a>傳回

* 反余弦值，其為`x`
* `null`如果 `x` <-1 或 `x` > 1