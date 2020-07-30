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
ms.openlocfilehash: 6d325861c7264c77535caf6df6c363ec801dd697
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347199"
---
# <a name="isnotempty"></a>isnotempty()

`true`如果引數不是空字串，則傳回，而且不是 null。

```kusto
isnotempty("") == false
```

## <a name="syntax"></a>語法

`isnotempty(`[*值*]`)`

`notempty(`[*值*] `)`--的別名`isnotempty`
