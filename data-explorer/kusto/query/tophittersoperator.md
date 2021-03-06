---
title: hitters 前操作員-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的最 hitters 操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d95c981f999d0842a266702ad5fc733281d45a7d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245788"
---
# <a name="top-hitters-operator"></a>top-hitters 運算子

傳回前 *N* 個結果的近似值， (假設輸入) 的扭曲分佈。

```kusto
T | top-hitters 25 of Page by Views 
```

> [!NOTE]
> `top-hitters` 是近似的演算法，而且應該在使用大型資料執行時使用。 最高 hitters 的近似值是根據「 [計數-最小值」（Sketch）量值](https://en.wikipedia.org/wiki/Count%E2%80%93min_sketch) 。  

## <a name="syntax"></a>語法

*T* `| top-hitters` *NumberOfRows* `of` *sort_key* `[` `by` *運算式*`]`

## <a name="arguments"></a>引數

* *NumberOfRows*：要傳回的 *T* 的資料列數目。 您可以指定任何數值運算式。
* *sort_key*：用來排序資料列的資料行名稱。
* *運算式*： (選擇性) 將用於 hitters 估計的運算式。 
    * *expression*： top hitters 會傳回 *NumberOfRows* 資料列，其中最大值為 sum (*運算式*) 。 運算式可以是資料行，或任何其他評估為數字的運算式。 
    *  如果未提及 *運算式* ，則 hitters 演算法會計算 *排序索引鍵*的出現次數。  

## <a name="examples"></a>範例

### <a name="get-most-frequent-items"></a>取得最常用的專案 

下一個範例示範如何在2016年4月) 之前，尋找在維琪百科中有大部分頁面的前5種語言 (存取。 

```kusto
PageViews
| where Timestamp > datetime(2016-04-01) and Timestamp < datetime(2016-05-01) 
| top-hitters 5 of Language 
```

|語言|approximate_count_Language|
|---|---|
|en|1539954127|
|zh|339827659|
|de|262197491|
|ru|227003107|
|fr|207943448|

### <a name="get-top-hitters-based-on-column-value"></a>根據資料行值取得 top hitters

下一個範例示範如何在2016年的維琪百科中尋找大部分的英文網頁。 此查詢會使用「Views」 (的整數) 來計算頁面熱門程度 () 的視圖數目。 

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
|JAVA_ (programming_language) |16489491|
|United_States|13928841|
|維琪 百科|13584915|
|Donald_Trump|12376448|
|YouTube|11917252|
|The_Revenant_ (2015_film) |10714263|
|Star_Wars： _The_Force_Awakens|9770653|
|入口網站： Current_events|9578000|
