---
title: 提取json() - Azure數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的提取json()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6177a1c8a6ed4390093e6f6fd24c5f5e9fd04f8a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515329"
---
# <a name="extractjson"></a>extractjson()

使用路徑運算式從 JSON 文字取出指定的項目。 

此函式可選擇性地將擷取出的字串轉換為特定類型。

```kusto
extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))
```

**語法**

`extractjson(`*jsonPath* `,` *資料來源*`)` 

**引數**

* *jsonPath:JsonPath*字串,用於在 JSON 文檔中定義訪問器。
* *數據來源*:JSON 文檔。

**傳回**

此函式會對包含有效 JSON 字串的 dataSource 執行 JsonPath 查詢，並根據第三個引數選擇性地將該值轉換成另一種類型。

**範例**

支架表示法和點`.`( ) 符號`]``[`等效:

```kusto
T 
| extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) 

T
| extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) 
```

### <a name="json-path-expressions"></a>JSON 路徑運算式

|||
|---|---|
|`$`|根物件|
|`@`|目前的物件|
|`.` 或 `[ ]` | 子系|
|`[ ]`|陣列註標|

*(我們目前未實作萬用字元、遞迴、聯集或配量。)*


**效能秘訣**

* 先套用 where 子句，再使用 `extractjson()`
* 請考慮改為搭配使用規則運算式相符項目與 [extract](extractfunction.md) 。 如果 JSON 是從範本產生，這麼做可以執行的非常快並且有效。
* 如果您需要從 JSON 中擷取多個值，請使用 `parse_json()` 。
* 請考慮在擷取 JSON 時透過將資料行的類型宣告為動態以剖析 JSON。