---
title: extract_all() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的extract_all()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f7623a064e272f8b96ca25cc6af47318e26dfb93
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515414"
---
# <a name="extract_all"></a>extract_all()

從文字字串獲取[正則運算式](./re2.md)的所有匹配項。

或者,可以檢索匹配組的子集。

```kusto
print extract_all(@"(\d+)", "a set of numbers: 123, 567 and 789") == dynamic(["123", "567", "789"])
```

**語法**

`extract_all(`*正規表示式*`,`=*捕獲群組*`,`=*文字*`)`

**引數**

* *正規表示式*:[正規表示式](./re2.md)。 正則表達式必須至少有一個捕獲組,並且小於或等於 16 個捕獲組。
* *捕獲組*:(可選)。 指示要提取的捕獲組的動態陣列常量。 有效值是從正則表達式中捕獲組的 1 到數。 也允許命名捕獲組(請參閱示例部分)。
* *文字*`string`: 要搜尋的 。

**傳回**

如果*regex*在*文字*中找到匹配項 :返回動態陣列,包括針對指示捕獲組*捕獲群組*(或*正則運算式*中擷取的所有組)的所有匹配項。
如果*捕獲組*數為 1:則返回的數組具有匹配值的單個維度。
如果*擷取群組*數大於 1:傳回的陣列是每個*捕獲群組*選擇的多值匹配的二維集合(如果省略*捕獲群組*,則*註冊中*存在的所有捕獲組) 

如果沒有符合: `null`. 

**範例**

### <a name="extracting-single-capture-group"></a>擷取單一個擷取群組
下面的示例返回 GUID 的十六進位元組表示(兩個十六進位數位)。

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"([\da-f]{2})", Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|{"82","b8","be","df","a7","4b","d1","8f","63","24","廣告","26","d3","14","49"]|

### <a name="extracting-several-capture-groups"></a>擷取多個擷取群組 
下一個範例使用具有 3 個捕獲組的正則運算式將每個 GUID 部分拆分為第一個字母、最後一個字母和中間的任何內容。

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|["8","2b8be2","d","d","fa","7","4","bd","1",""8","f6","3","2","4ad26d3144","9"]。|

### <a name="extracting-subset-of-capture-groups"></a>擷取擷取群組的子集

下一個範例展示如何選擇捕獲組的子集:在這種情況下,正則運算式匹配到第一個字母、最後一個字母和所有其餘部分中,而*捕獲組*參數僅用於選擇第一個和最後一部分。 

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", dynamic([1,3]), Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[{"8","d","d","7","{4","1","{8","3","2","9"]|


### <a name="using-named-capture-groups"></a>使用命名擷取群組

您可以在extract_all()中利用命名的RE2捕獲組。 在下面的範例中 -*捕獲群組*同時使用捕獲組索引和命名捕獲組引用來獲取匹配值。

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(?P<first>\w)(?P<middle>\w+)(?P<last>\w)", dynamic(['first',2,'last']), Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|["8","2b8be2","d","d","fa","7","4","bd","1",""8","f6","3","2","4ad26d3144","9"]。|