---
title: '雜湊 ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中的雜湊 ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d840ba106079a85435ee88f8b73cfc6daa7e4c5b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252328"
---
# <a name="hash"></a>hash()

傳回輸入值的雜湊值。

## <a name="syntax"></a>語法

`hash(`*來源* [ `,` *mod*]`)`

## <a name="arguments"></a>引數

* *來源*：要雜湊的值。
* *mod*：要套用到雜湊結果的選擇性模組值，讓輸出值介於 `0` 和 *mod* -1 之間

## <a name="returns"></a>傳回

給定的純量的雜湊值，如果指定的) ，則模數指定的 mod 值 (。

> [!WARNING]
> 用來計算雜湊的演算法為 xxhash。
> 此演算法未來可能會變更，唯一的保證是在單一查詢中，此方法的所有調用都使用相同的演算法。
> 因此，建議您不要將結果儲存 `hash()` 在資料表中。 如果需要保存雜湊值，請改用 [hash_sha256 ( # B1 ](./sha256hashfunction.md) 或 [Hash_md5 ( # B3 ](./md5hashfunction.md) 。 請注意，相較于) ，這些函數比較複雜 `hash()` 。

## <a name="examples"></a>範例

```kusto
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```

下列範例會使用雜湊函式在10% 的資料上執行查詢，當假設值是在此範例 StartTime 值中統一散發 (時，使用雜湊函數來取樣資料會很有用) 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where hash(StartTime, 10) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```
