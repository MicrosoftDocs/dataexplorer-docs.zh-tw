---
title: 包() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 pack()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a7b43be96f272ab929434f10cac910bd4072e650
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511827"
---
# <a name="pack"></a>pack()

從名稱`dynamic`和值清單中創建物件(屬性包)。

別名要`pack_dictionary()`起作用。

**語法**

`pack(`*秒1* `,` *值1* `,` *秒2*`,`*值2*`,... )`

**引數**

* 鍵與值的交替清單(列表的總長度必須為偶數)
* 所有鍵必須為非空常量字串

**範例**

下列範例會傳回 `{"Level":"Information","ProcessID":1234,"Data":{"url":"www.bing.com"}}`：

```kusto
pack("Level", "Information", "ProcessID", 1234, "Data", pack("url", "www.bing.com"))
```

讓我們拿2個表,短信和短信:

表短信 

|來源編號 |目標編號| 查爾斯伯爵
|---|---|---
|555-555-1234 |555-555-1212 | 46 
|555-555-1234 |555-555-1213 | 50 
|555-555-1212 |555-555-1234 | 32 

表 MmSMessages 

|來源編號 |目標編號| 附加網路大小 | 附加類型 | 附加名稱
|---|---|---|---|---
|555-555-1212 |555-555-1213 | 200 | JPEG | 圖片1
|555-555-1234 |555-555-1212 | 250 | JPEG | 圖片2
|555-555-1234 |555-555-1213 | 300 | png | 圖片3

下列查詢：
```kusto
SmsMessages 
| extend Packed=pack("CharsCount", CharsCount) 
| union withsource=TableName kind=inner 
( MmsMessages 
  | extend Packed=pack("AttachmnetSize", AttachmnetSize, "AttachmnetType", AttachmnetType, "AttachmnetName", AttachmnetName))
| where SourceNumber == "555-555-1234"
``` 

傳回：

|TableName |來源編號 |目標編號 | 包裝
|---|---|---|---
|短信|555-555-1234 |555-555-1212 | ["字元計數": 46]
|短信|555-555-1234 |555-555-1213 | ["字元計數": 50]
|MmsMessages|555-555-1234 |555-555-1212 | {"附加mnetSize":250,"附加網類型":"jpeg","附加網名稱":"Pic2"]
|MmsMessages|555-555-1234 |555-555-1213 | {"附加網络大小":300,"附加網類型":"png","附加網名稱":"Pic3"]