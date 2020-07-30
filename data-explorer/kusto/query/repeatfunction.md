---
title: 重複（）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的重複（）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0d488ac96cd3db2161761ea837d5490d25cfc920
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345822"
---
# <a name="repeat"></a>repeat()

產生保存一系列相等值的動態陣列。

## <a name="syntax"></a>語法

`repeat(`*值* `,`*計數*`)` 

## <a name="arguments"></a>引數

* *值*：產生之陣列中的元素值。 *值*的類型可以是 boolean、integer、long、real、datetime 或 timespan。   
* *count*：產生之陣列中的元素計數。 *計數*必須是整數。
如果*count*等於零，則會傳回空陣列。
如果*count*小於零，則會傳回 null 值。 

## <a name="examples"></a>範例

下列範例會傳回 `[1, 1, 1]`：

```kusto
T | extend r = repeat(1, 3)
```