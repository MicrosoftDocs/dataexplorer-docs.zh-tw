---
title: hash （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的雜湊（）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 16b570d996148f1dad9e285b3c2da24d136b20c4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347573"
---
# <a name="hash"></a>hash()

傳回輸入值的雜湊值。

## <a name="syntax"></a>語法

`hash(`*來源*[ `,` *mod*]`)`

## <a name="arguments"></a>引數

* *來源*：要雜湊的值。
* *mod*：要套用至雜湊結果的選擇性模組值，讓輸出值介於 `0` 和*mod* -1 之間

## <a name="returns"></a>傳回

給定純量的雜湊值，模數給定的 mod 值（如果有指定）。

> [!WARNING]
> 用來計算雜湊的演算法是 xxhash。
> 這個演算法未來可能會變更，而且唯一的保證是在單一查詢中，此方法的所有調用都會使用相同的演算法。
> 因此，建議您不要將的結果儲存 `hash()` 在資料表中。 如果需要保存雜湊值，請改用[hash_sha256 （）](./sha256hashfunction.md)或[hash_md5 （）](./md5hashfunction.md) 。 請注意，計算這些函數比更複雜 `hash()` 。

## <a name="examples"></a>範例

```kusto
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```

下列範例使用雜湊函式來執行10% 資料的查詢，在假設值已一致散發時，使用雜湊函式來取樣資料會很有説明（在此範例中，StartTime 值）

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where hash(StartTime, 10) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```
