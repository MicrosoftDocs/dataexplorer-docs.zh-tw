---
title: 不是（）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 not （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fed2a55c8fa1c7689c087ccdeaa64ff576bea401
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346587"
---
# <a name="not"></a>not()

反轉其 `bool` 引數的值。

```kusto
not(false) == true
```

## <a name="syntax"></a>語法

`not(`*expr*`)`

## <a name="arguments"></a>引數

* *expr*： `bool` 要反轉的運算式。

## <a name="returns"></a>傳回

傳回其引數的反轉邏輯值 `bool` 。