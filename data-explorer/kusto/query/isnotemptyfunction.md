---
title: isnotempty （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 isnotempty （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5a21031b07df3a4fa654fd13eb3761618308337b
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717337"
---
# <a name="isnotempty"></a>isnotempty()

`true`如果引數不是空字串，則傳回，而且不是 null。

```kusto
isnotempty("") == false
```

**語法**

`isnotempty(`[*值*]`)`

`notempty(`[*值*] `)`--的別名`isnotempty`
