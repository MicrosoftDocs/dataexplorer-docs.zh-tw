---
title: pack （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 pack （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 560f7ca9f423eb57fd9be0478e0e51dc2ce26755
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346502"
---
# <a name="pack"></a>pack()

`dynamic`從名稱和值的清單建立物件（屬性包）。

函式 `pack_dictionary()` 的別名。

## <a name="syntax"></a>語法

`pack(`*key1* `,`*value1* `,`*key2* `,`*value2*`,... )`

## <a name="arguments"></a>引數

* 索引鍵和值的替代清單（清單的總長度必須是偶數）
* 所有索引鍵都必須是非空白的常數位串

## <a name="examples"></a>範例

下列範例會傳回 `{"Level":"Information","ProcessID":1234,"Data":{"url":"www.bing.com"}}`：

```kusto
pack("Level", "Information", "ProcessID", 1234, "Data", pack("url", "www.bing.com"))
```

讓我們採用2個數據表，SmsMessages 和 MmsMessages：

資料表 SmsMessages 

|SourceNumber |TargetNumber| CharsCount
|---|---|---
|555-555-1234 |555-555-1212 | 46 
|555-555-1234 |555-555-1213 | 50 
|555-555-1212 |555-555-1234 | 32 

資料表 MmsMessages 

|SourceNumber |TargetNumber| AttachmentSize | AttachmentType | AttachmentName
|---|---|---|---|---
|555-555-1212 |555-555-1213 | 200 | jpeg | Pic1
|555-555-1234 |555-555-1212 | 250 | jpeg | Pic2
|555-555-1234 |555-555-1213 | 300 | png | Pic3

下列查詢：
```kusto
SmsMessages 
| extend Packed=pack("CharsCount", CharsCount) 
| union withsource=TableName kind=inner 
( MmsMessages 
  | extend Packed=pack("AttachmentSize", AttachmentSize, "AttachmentType", AttachmentType, "AttachmentName", AttachmentName))
| where SourceNumber == "555-555-1234"
``` 

傳回：

|TableName |SourceNumber |TargetNumber | 包裝
|---|---|---|---
|SmsMessages|555-555-1234 |555-555-1212 | {"CharsCount"： 46}
|SmsMessages|555-555-1234 |555-555-1213 | {"CharsCount"： 50}
|MmsMessages|555-555-1234 |555-555-1212 | {"AttachmentSize"：250，"AttachmentType"： "jpeg"，"AttachmentName"： "Pic2"}
|MmsMessages|555-555-1234 |555-555-1213 | {"AttachmentSize"：300，"AttachmentType"： "png"，"AttachmentName"： "Pic3"}
