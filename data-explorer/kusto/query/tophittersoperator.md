---
title: 頂級人物運算符 - Azure 資料資源管理員 |微軟文件
description: 本文介紹了 Azure 數據資源管理器中的頂級抖動運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7105bd4f9818deab1f0fa5dbe25dbb2d5b1b4afc
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663113"
---
# <a name="top-hitters-operator"></a>top-hitters 運算子

返回*前*N 結果的近似值(假設輸入的傾斜分佈)。

```kusto
T | top-hitters 25 of Page by Views 
```

**語法**

*T* `| top-hitters` *行*`of`*數*`[`sort_key*expression*`by`表示式`]`

**引數**

* *行數*:要返回的*T*行數。 您可以指定任何數字運算式。
* *sort_key*: 用於對行進行排序的欄的名稱。
* *表達式*:( 可選) 將用於頂級抖動估計的運算式。 
    * *表示式*: 頂端抖動會傳回有近似最大與 (*表示式*) 的*Rows 的 Rows 數*。 表達式可以是列,也可以是計算為數位的任何其他運算式。 
    *  如果未提及*表達式*,則頂級哈希演演演算法將計算*排序鍵*的匹配項的發生。  

**注意事項**

`top-hitters`是一種近似演演演算法,在使用大數據運行時應使用。 頂級抖動的近似值基於[計數-最小草圖](https://en.wikipedia.org/wiki/Count%E2%80%93min_sketch)演演演算法。  

**範例**

## <a name="getting-top-hitters-most-frequent-items"></a>取得頂級擊球手(最頻繁的專案) 

下一個示例演示如何查找維琪百科中大多數頁面的前 5 種語言(2016 年 4 月之後訪問)。 

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

## <a name="getting-top-hitters-based-on-column-value-"></a>取得頂級擊球手(基於列值) |

下一個範例展示如何查找 2016 年維琪百科中查看次數最多的英文頁面。 查詢使用「檢視」(整數數)來計算頁面受歡迎程度(視圖數)。 

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
|Java_(programming_language)|16489491|
|United_States|13928841|
|維琪 百科|13584915|
|Donald_Trump|12376448|
|YouTube|11917252|
|The_Revenant_(2015_film)|10714263|
|Star_Wars:_The_Force_Awakens|9770653|
|門戶:Current_events|9578000|