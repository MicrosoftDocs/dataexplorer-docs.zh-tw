---
title: '不 ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明在 Azure 資料總管中不 ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3224c48b963c051d0d65d27a2e64ec8317be30c3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252177"
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

傳回其引數的反向邏輯值 `bool` 。