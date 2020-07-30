---
title: hitters 運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的熱門 hitters 操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: babb4e023d29c7894661e3acf2c0a09e753011c2
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340814"
---
# <a name="top-hitters-operator"></a>top-hitters 運算子

傳回前*N*個結果的近似值（假設輸入的扭曲分佈）。

```kusto
T | top-hitters 25 of Page by Views 
```

## <a name="syntax"></a>語法

*T* `| top-hitters` *NumberOfRows* `of` *sort_key* `[` `by` *運算式*`]`

## <a name="arguments"></a>引數

* *NumberOfRows*：要傳回的*T*資料列數目。 您可以指定任何數值運算式。
* *sort_key*：用來排序資料列的資料行名稱。
* *expression*：（選擇性）將用於最上層 hitters 估計的運算式。 
    * *expression*： hitters 會傳回*NumberOfRows*的資料列，其最大值為 sum （*expression*）。 Expression 可以是一個資料行，或任何其他評估為數字的運算式。 
    *  如果未提及*expression* ，則 hitters 演算法會計算*排序索引鍵*的出現次數。  

**備註**

`top-hitters`是近似值演算法，而且應該在以大型資料執行時使用。 頂尖 hitters 的近似值是以「[計數-最小-草圖](https://en.wikipedia.org/wiki/Count%E2%80%93min_sketch)」演算法為基礎。  

## <a name="example"></a>範例

## <a name="getting-top-hitters-most-frequent-items"></a>取得最上層 hitters （最常見的專案） 

下一個範例示範如何在維琪百科中尋找大部分頁面的前5種語言（在2016年4月之後存取）。 

```kusto
PageViews
| where Timestamp > datetime(2016-04-01) and Timestamp < datetime(2016-05-01) 
| top-hitters 5 of Language 
```

|Language|approximate_count_Language|
|---|---|
|en|1539954127|
|zh|339827659|
|de|262197491|
|ru|227003107|
|fr|207943448|

## <a name="getting-top-hitters-based-on-column-value-"></a>取得最上層 hitters （根據資料行值） * * *

下一個範例示範如何尋找2016年維琪百科觀看的大部分英文頁面。 此查詢會使用 [Views] （整數）來計算頁面最普及程度（Views 數目）。 

```kusto
PageViews
| where Timestamp > datetime(2016-01-01)
| where Language == "en"
| where Page !has 'Special'
| top-hitters 10 of Page by Views
```

|頁面|approximate_sum_Views|
|---|---|
|Main_Page|1325856754|
|Web_scraping|43979153|
|JAVA_ （programming_language）|16489491|
|United_States|13928841|
|Wikipedia|13584915|
|Donald_Trump|12376448|
|YouTube|11917252|
|The_Revenant_ （2015_film）|10714263|
|Star_Wars： _The_Force_Awakens|9770653|
|入口網站： Current_events|9578000|