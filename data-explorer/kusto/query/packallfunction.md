---
title: 'pack_all ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 pack_all ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0c4ce80c00df4c9c6e257fbe1f8a93802f54438e
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87802939"
---
# <a name="pack_all"></a>pack_all()

`dynamic`從表格式運算式的所有資料行建立物件 (屬性包) 。

> [!NOTE]
> 傳回之物件的標記法不保證會在執行之間以位元組層級相容。 例如，出現在包中的屬性可能會以不同的順序出現。

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
