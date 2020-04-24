---
title: format_bytes() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 format_bytes()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5d5bfa234e001f498737b9df88274096f8592f52
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515159"
---
# <a name="format_bytes"></a>format_bytes()

將數位格式化為以位元組為單位表示數據大小的字串。

```kusto
format_bytes(1024) == '1 KB'"
```

**語法**

`format_bytes(`*值*`,`[*精確度*`,`*]* 單位 *`)`

**引數**

* `value`:要格式化為位元組的資料大小的數位。
* `precision`:(可選) 值將捨入到的數字數。 (預設值為 0)。
* `units`: (選擇性的) 字串格式將使用的目標`Bytes`資料 大小的單位`KB``MB``GB``TB``PB`(、、、、、、、、、、格式)。 如果參數為空 - 將根據輸入值自動選擇單位。

**傳回**

具有格式結果的字串。

**範例**

```kusto
print 
v1 = format_bytes(564),
v2 = format_bytes(10332, 1),
v3 = format_bytes(20010332),
v4 = format_bytes(20010332, 2),
v5 = format_bytes(20010332, 0, "KB")
```

|v1|v2|v3|v4|v5|
|---|---|---|---|---|
|564 位元組|10.1 KB|19 MB|19.08 MB|19541 KB|