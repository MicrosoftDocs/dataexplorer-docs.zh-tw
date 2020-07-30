---
title: sqrt （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 sqrt （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 76f5c8c5f8c1a0b9f685ae88df1ab624dc446150
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350956"
---
# <a name="sqrt"></a>sqrt()

傳回平方根函數。  

## <a name="syntax"></a>語法

`sqrt(`*x*`)`

## <a name="arguments"></a>引數

* *x*：實數 >= 0。

## <a name="returns"></a>傳回

* 像是 `sqrt(x) * sqrt(x) == x`
* 如果引數為負數或無法轉換為 `real` 值，則為 `null`。 