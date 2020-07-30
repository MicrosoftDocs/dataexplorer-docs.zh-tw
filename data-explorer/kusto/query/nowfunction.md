---
title: now （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 now （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9beae6edd1361715dfe84f851ca0a9bb69f4299c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346570"
---
# <a name="now"></a>now()

傳回目前的 UTC 時鐘時間，選擇性地以給定的 timespan 來位移。
此函式可在陳述式中多次使用，而且所參考的時鐘時間在所有執行個體皆相同。

```kusto
now()
now(-2d)
```

## <a name="syntax"></a>語法

`now(`[*offset*]`)`

## <a name="arguments"></a>引數

* *offset*： `timespan` ，已加入至目前的 UTC 時鐘時間。 預設值︰0。

## <a name="returns"></a>傳回

目前的 UTC 時鐘時間，格式為 `datetime`。

`now()` + *投影* 

## <a name="example"></a>範例

決定從述詞所識別的事件算起的間隔︰

```kusto
T | where ... | extend Elapsed=now() - Timestamp
```