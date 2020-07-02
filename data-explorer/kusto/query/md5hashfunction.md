---
title: hash_md5 （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 hash_md5 （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/29/2020
ms.openlocfilehash: c09f30a4f13f16e15cfcc826f6976f1208fdabf1
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793618"
---
# <a name="hash_md5"></a>hash_md5 （）

傳回輸入值的 MD5 雜湊值。

**語法**

`hash_md5(`*來源*`)`

**引數**

* *來源*：要雜湊的值。

**傳回**

指定純量的 MD5 雜湊值，編碼為十六進位字串（字元字串，每兩個都代表0到255之間的一個十六進位數位）。

> [!WARNING]
> 此函式（MD5）所使用的演算法保證不會在未來進行修改，但非常複雜而無法計算。 在單一查詢期間，需要「輕量」雜湊函式的使用者，建議改用函數[雜湊（）](./hashfunction.md) 。

**範例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print 
h1=hash_md5("World"),
h2=hash_md5(datetime(2020-01-01))
```

|h1|h2|
|---|---|
|f5a7924e621e84c9280a9a27e1bcb7f6|786c530672d1f8db31fee25ea8a9390b|


下列範例會 `hash_md5()` 根據狀態的 MD5 雜湊值，使用函數來匯總 StormEvents。 

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize StormCount = count() by State, StateHash=hash_md5(State)
| top 5 by StormCount
```

|State|StateHash|StormCount|
|---|---|---|
|德克薩斯州|3b00dbe6e07e7485a1c12d36c8e9910a|4701|
|KANSAS|e1338d0ac8be43846cf9ae967bd02e7f|3166|
|愛荷華州|6d4a7c02942f093576149db764d4e2d2|2337|
|伊利諾州|8c00d9e0b3fcd55aed5657e42cc40cf1|2022|
|MISSOURI|2d82f0c963c0763012b2539d469e5008|2016|
