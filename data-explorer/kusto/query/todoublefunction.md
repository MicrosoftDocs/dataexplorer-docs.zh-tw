---
title: 'todouble ( # A1/toreal ( # A3-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 todouble ( # A1/toreal ( # A3。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8e93e86814adf2789d01e03173196468f085b7c2
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804095"
---
# <a name="todouble-toreal"></a>todouble(), toreal()

將輸入轉換為類型的值 `real` 。  (`todouble()` 和 `toreal()` 是同義字。 ) 

```kusto
toreal("123.4") == 123.4
```

> [!NOTE]
> 偏好盡可能使用[double ( # A1 或 real ( # B3](./scalar-data-types/real.md) 。

## <a name="syntax"></a>語法

`toreal(`*Expr* `)` 
 Expr `todouble(`*Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：其值將轉換成類型值的運算式 `real` 。

## <a name="returns"></a>傳回

如果轉換成功，則結果會是類型的值 `real` 。
如果轉換不成功，則結果會是值 `real(null)` 。
