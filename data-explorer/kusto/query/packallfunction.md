---
title: 'pack_all ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 pack_all。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 547c9960d9a9f04e57f1b5ff0cdebada449b1b4b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249707"
---
# <a name="pack_all"></a>pack_all()

`dynamic`從表格式運算式的所有資料行，建立 (屬性包) 的物件。

> [!NOTE]
> 傳回的物件標記法不保證在執行之間是位元組層級相容的。 例如，出現在包中的屬性可能會以不同的順序出現。

## <a name="syntax"></a>語法

`pack_all()`

## <a name="examples"></a>範例

給定資料表 SmsMessages 

|SourceNumber |TargetNumber| CharsCount
|---|---|---
|555-555-1234 |555-555-1212 | 46 
|555-555-1234 |555-555-1213 | 50 
|555-555-1212 |555-555-1234 | 32 

下列查詢：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(SourceNumber:string,TargetNumber:string,CharsCount:long)
[
'555-555-1234','555-555-1212',46,
'555-555-1234','555-555-1213',50,
'555-555-1212','555-555-1234',32
]
| extend Packed=pack_all()
```

傳回：

|TableName |SourceNumber |TargetNumber | 包裝
|---|---|---|---
|SmsMessages|555-555-1234 |555-555-1212 | {"SourceNumber"： "555-555-1234"，"TargetNumber"： "555-555-1212"，"CharsCount"： 46}
|SmsMessages|555-555-1234 |555-555-1213 | {"SourceNumber"： "555-555-1234"，"TargetNumber"： "555-555-1213"，"CharsCount"： 50}
|SmsMessages|555-555-1212 |555-555-1234 | {"SourceNumber"： "555-555-1212"，"TargetNumber"： "555-555-1234"，"CharsCount"： 32}
