---
title: 'extractjson ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 extractjson ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4891e3b906de540fb2b4939e65359829fd442f7f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247508"
---
# <a name="extractjson"></a>extractjson()

使用路徑運算式從 JSON 文字取出指定的項目。 

此函式可選擇性地將擷取出的字串轉換為特定類型。

```kusto
extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))
```

## <a name="syntax"></a>語法

`extractjson(`*jsonPath* `,`*dataSource*`)` 

## <a name="arguments"></a>引數

* *jsonPath*：在 JSON 檔中定義存取子的 jsonPath 字串。
* *dataSource*： JSON 檔。

## <a name="returns"></a>傳回

此函式會對包含有效 JSON 字串的 dataSource 執行 JsonPath 查詢，並根據第三個引數選擇性地將該值轉換成另一種類型。

## <a name="example"></a>範例

`[`括弧 `]` notatation 和點 (`.`) 標記法是相等的：

```kusto
T 
| extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) 

T
| extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) 
```

### <a name="json-path-expressions"></a>JSON 路徑運算式

|路徑運算式|描述|
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