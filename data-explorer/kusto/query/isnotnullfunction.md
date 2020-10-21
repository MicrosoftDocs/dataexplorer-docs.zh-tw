---
title: 'isnotnull ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 isnotnull ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c198bd34161c683c6e5c6f1bde2990c0605122c8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253273"
---
# <a name="isnotnull"></a>isnotnull()

`true`如果引數不是 null，則會傳回。

## <a name="syntax"></a>語法

`isnotnull(`[*值*]`)`

`notnull(`[*值*] `)` -別名 `isnotnull`

## <a name="example"></a>範例

```kusto
T | where isnotnull(PossiblyNull) | count
```

請注意，還有其他方式能達成此效果︰

```kusto
T | summarize count(PossiblyNull)
```