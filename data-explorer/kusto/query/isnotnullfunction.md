---
title: isnotnull （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 isnotnull （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8ff472919ecda9550e7e0bcd6b403c605d029bfb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347182"
---
# <a name="isnotnull"></a>isnotnull()

`true`如果引數不是 null，則傳回。

## <a name="syntax"></a>語法

`isnotnull(`[*值*]`)`

`notnull(`[*值*] `)`-別名`isnotnull`

## <a name="example"></a>範例

```kusto
T | where isnotnull(PossiblyNull) | count
```

請注意，還有其他方式能達成此效果︰

```kusto
T | summarize count(PossiblyNull)
```