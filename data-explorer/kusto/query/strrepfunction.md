---
title: strrep() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 strrep()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 39b398e8fadb400c25cfeb97487c2ecf0669ad83
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506710"
---
# <a name="strrep"></a>strrep()

重複給定[字串](./scalar-data-types/string.md)提供的次數。

* 如果第一個或第三個參數不是字串類型,它將強制轉換為字串。

**語法**

`strrep(`*值*,*乘數*, [*分隔符*]`)`

**引數**

* *值*:輸入表示式
* *乘數*: 正整數值 (從 1 到 1024)
* *分隔符*:選擇字串表示式(預設值:空字串)

**傳回**

重複指定次數的值,與*分隔串*聯。

如果*乘數*大於最大允許值 (1024),則輸入字串將重複 1024 次。
 
**範例**

```kusto
print from_str = strrep('ABC', 2), from_int = strrep(123,3,'.'), from_time = strrep(3s,2,' ')
```

|from_str|from_int|from_time|
|---|---|---|
|ABCABC|123.123.123|00:00:03 00:00:03|