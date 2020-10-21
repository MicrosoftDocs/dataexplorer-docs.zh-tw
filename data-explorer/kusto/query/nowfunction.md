---
title: '現在 ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文現在將說明 Azure 資料總管中 ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 63b99b4774c46872b83e526ca00a5d974f0b0c19
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249802"
---
# <a name="now"></a>now()

傳回目前的 UTC 時鐘時間，選擇性地以指定的 timespan 為位移。
此函式可在陳述式中多次使用，而且所參考的時鐘時間在所有執行個體皆相同。

```kusto
now()
now(-2d)
```

## <a name="syntax"></a>語法

`now(`[*位移*]`)`

## <a name="arguments"></a>引數

* *位移*： `timespan` ，加入至目前的 UTC 時鐘時間。 預設值︰0。

## <a name="returns"></a>傳回

目前的 UTC 時鐘時間，格式為 `datetime`。

`now()` + *抵消* 

## <a name="example"></a>範例

決定從述詞所識別的事件算起的間隔︰

```kusto
T | where ... | extend Elapsed=now() - Timestamp
```