---
title: pack_all() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的pack_all()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3c6a22b656e28b8b7113864e0b3f9636a4fb364d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511929"
---
# <a name="pack_all"></a>pack_all()

從表格`dynamic`運算式的所有列創建物件(屬性包)。

**語法**

`pack_all()`

**範例**

給定表 SmsMessages 

|來源編號 |目標編號| 查爾斯伯爵
|---|---|---
|555-555-1234 |555-555-1212 | 46 
|555-555-1234 |555-555-1213 | 50 
|555-555-1212 |555-555-1234 | 32 

下列查詢：
```kusto
SmsMessages | extend Packed=pack_all()
``` 

傳回：

|TableName |來源編號 |目標編號 | 包裝
|---|---|---|---
|短信|555-555-1234 |555-555-1212 | {"源號":"555-555-1234","目標號碼":"555-555-1212","字元計數":46]
|短信|555-555-1234 |555-555-1213 | {"源號":"555-555-1234","目標號碼":"555-555-1213","字元計數":50]
|短信|555-555-1212 |555-555-1234 | {"源號":"555-555-1212","目標號碼":"555-555-1234","字元計數":32]