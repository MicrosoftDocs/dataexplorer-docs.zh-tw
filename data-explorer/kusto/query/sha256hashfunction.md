---
title: hash_sha256 （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 hash_sha256 （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b813ce4c0901ef66177e8e7bdaa42a1744bd5912
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351109"
---
# <a name="hash_sha256"></a>hash_sha256()

傳回輸入值的 sha256 雜湊值。

## <a name="syntax"></a>語法

`hash_sha256(`*來源*`)`

## <a name="arguments"></a>引數

* *來源*：要雜湊的值。

## <a name="returns"></a>傳回

指定純量的 sha256 雜湊值，編碼為十六進位字串（字元字串，每兩個都代表0到255之間的一個十六進位數位）。

> [!WARNING]
> 此函式（SHA256）所使用的演算法保證不會在未來進行修改，但對計算而言非常複雜。 在單一查詢期間，需要「輕量」雜湊函式的使用者，建議改用函數[雜湊（）](./hashfunction.md) 。

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

|State|StateHash|StormCount|
|---|---|---|
|德克薩斯州|9087f20f23f91b5a77e8406846117049029e6798ebbd0d38aea68da73a00ca37|4701|
|KANSAS|c80e328393541a3181b258cdb4da4d00587c5045e8cf3bb6c8fdb7016b69cc2e|3166|
|愛荷華州|f85893dca466f779410f65cd904fdc4622de49e119ad4e7c7e4a291ceed1820b|2337|
|伊利諾州|ae3eeabfd7eba3d9a4ccbfed6a9b8cff269dc43255906476282e0184cf81b7fd|2022|
|MISSOURI|d15dfc28abc3ee73b7d1f664a35980167ca96f6f90e034db2a6525c0b8ba61b1|2016|
