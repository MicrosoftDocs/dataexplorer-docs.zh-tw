---
title: 前（）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的前（）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3f3d58a7b29731125407eec7429967d1f90f5b7e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349766"
---
# <a name="ago"></a>ago()

從目前的 UTC 時鐘時間減去指定的時間範圍。

```kusto
ago(1h)
ago(1d)
```

和 `now()`一樣，此函式可在陳述式中多次使用，而且所參考的 UTC 時鐘時間在所有具現化中皆相同。

## <a name="syntax"></a>語法

`ago(`*a_timespan*`)`

## <a name="arguments"></a>引數

* a_timespan︰** 要從目前的 UTC 時鐘時間 (`now()`) 減去的間隔。

## <a name="returns"></a>傳回

`now() - a_timespan`

## <a name="example"></a>範例

時間戳記為過去一小時的所有資料列︰

```kusto
T | where Timestamp > ago(1h)
```