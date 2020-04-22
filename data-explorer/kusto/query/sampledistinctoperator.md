---
title: 樣本不同的運算子 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的範例不同運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b6d6c77aef3a7e2c6d99af792062d9f1a6215f51
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663639"
---
# <a name="sample-distinct-operator"></a>sample-distinct 運算子

傳回單一資料行，其中包含所要求資料行的相異值數目上限。 

運算子的預設(目前僅)風格試圖儘快返回答案(而不是嘗試創建一個公平的樣本)

```kusto
T | sample-distinct 5 of DeviceId
```

**語法**

*T* `| sample-distinct` *個值*`of`欄*位*

**引數**
* *值數*:要返回的*T*的不同值數。 您可以指定任何數字運算式。

**技巧**

 通過放入`sample-distinct`let 語句和`in`以後使用 運算元進行篩選,可以很方便地對總體進行採樣(請參閱示例) 

 如果想要頂級值,而不僅僅是示例,則可以使用[頂級抖動](tophittersoperator.md)運算符號 

 如果要對資料列(而不是特定欄位值)進行取樣,請參閱[範例運算子](sampleoperator.md)

**範例**  

從總體取得 10 個不同的值

```kusto
StormEvents | sample-distinct 10 of EpisodeId

```

為母體取樣並在知道摘要不會超過查詢限制的情況下執行進一步的運算。 

```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents 
| where EpisodeId in (sampleEpisodes) 
| summarize totalInjuries=sum(InjuriesDirect) by EpisodeId
```