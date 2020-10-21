---
title: 'hash_sha256 ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 hash_sha256。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3d6cd8b71ac5abed2d56e567c992a15512141063
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242590"
---
# <a name="hash_sha256"></a>hash_sha256()

傳回輸入值的 sha256 雜湊值。

## <a name="syntax"></a>語法

`hash_sha256(`*源*`)`

## <a name="arguments"></a>引數

* *來源*：要雜湊的值。

## <a name="returns"></a>傳回

給定純量的 sha256 雜湊值（以十六進位字串編碼為十六進位字串） (字元字串，每兩個字元都代表0和 255) 之間的單一十六進位數位。

> [!WARNING]
> 此函式所使用的演算法 (SHA256) 保證不會在未來進行修改，但很複雜，無法進行計算。 建議在單一查詢期間需要「輕量」雜湊函數的使用者，改為使用函式 [雜湊 ( # B1 ](./hashfunction.md) 。

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print 
h1=hash_sha256("World"),
h2=hash_sha256(datetime(2020-01-01))
```

|h1|h2|
|---|---|
|78ae647dc5544d227130a0682a51e30bc7777fbb6d8a8f17007463a3ecd1d524|ba666752dc1a20eb750b0eb64e780cc4c968bc9fb8813461c1d7e750f302d71d|

下列範例會 `hash_sha256()` 根據狀態的 SHA256 雜湊值，使用函數來匯總 StormEvents。 

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize StormCount = count() by State, StateHash=hash_sha256(State)
| top 5 by StormCount desc
```

|狀態|StateHash|StormCount|
|---|---|---|
|德克薩斯州|9087f20f23f91b5a77e8406846117049029e6798ebbd0d38aea68da73a00ca37|4701|
|堪薩斯|c80e328393541a3181b258cdb4da4d00587c5045e8cf3bb6c8fdb7016b69cc2e|3166|
|愛荷華州|f85893dca466f779410f65cd904fdc4622de49e119ad4e7c7e4a291ceed1820b|2337|
|伊利諾州|ae3eeabfd7eba3d9a4ccbfed6a9b8cff269dc43255906476282e0184cf81b7fd|2022|
|密蘇里州|d15dfc28abc3ee73b7d1f664a35980167ca96f6f90e034db2a6525c0b8ba61b1|2016|
