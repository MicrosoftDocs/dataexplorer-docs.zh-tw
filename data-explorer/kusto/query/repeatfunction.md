---
title: '重複 ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中重複 ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cfe3c2704f7eb086319770925419a9877e39366b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243072"
---
# <a name="repeat"></a>repeat()

產生包含一系列相等值的動態陣列。

## <a name="syntax"></a>語法

`repeat(`*值* `,`*計數*`)` 

## <a name="arguments"></a>引數

* *值*：產生之陣列中的元素值。 值的類型可以是布林 *值* 、整數、long、real、datetime 或 timespan。   
* *count*：結果陣列中的元素計數。 *計數*必須是整數。
如果 *count* 等於零，則會傳回空陣列。
如果 *count* 小於零，則會傳回 null 值。 

## <a name="examples"></a>範例

下列範例會傳回 `[1, 1, 1]`：

```kusto
T | extend r = repeat(1, 3)
```