---
title: 'sqrt ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 sqrt ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 81b31d8a692a2f68cdb8dd68b5c5f1e75057d3b0
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242519"
---
# <a name="sqrt"></a>sqrt()

傳回平方根函數。  

## <a name="syntax"></a>語法

`sqrt(`*X*`)`

## <a name="arguments"></a>引數

* *x*：實數 >= 0。

## <a name="returns"></a>傳回

* 像是 `sqrt(x) * sqrt(x) == x`
* 如果引數為負數或無法轉換為 `real` 值，則為 `null`。 