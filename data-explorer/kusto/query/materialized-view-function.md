---
title: 'materialized_view ( # A1 (範圍函數) -Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 函式 materialized_view。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: e8dee5e1c33328eb70f984b20f61d4585abae550
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057078"
---
# <a name="materialized_view-function"></a>materialized_view ( # A1 函數

參考 [具體化視圖](../management/materialized-views/materialized-view-overview.md)的具體化部分。 

`materialized_view()`函數僅支援查詢*具體化*元件的方法，同時指定使用者願意容忍的延遲上限。 此選項不保證會傳回最新的記錄，但其效能應比查詢整個視圖的效能更高。 如果您願意犧牲某些有效的效能，例如在遙測儀表板中，此函式很有用。

<!--- csl --->
```
materialized_view('ViewName')
```

## <a name="syntax"></a>語法

`materialized_view(`*ViewName* `,`[*max_age*]`)`

## <a name="arguments"></a>引數

* *ViewName*：的名稱 `materialized view` 。
* *max_age*：選擇性。 如果未提供，則只會傳回視圖的 *具體化* 部分。 如果有提供，則函式會在上次具體化時間大於 [-max_age] 時，傳回視圖的 _具體化_ 部分 @now 。 否則，會傳回整個視圖 (與直接查詢 *ViewName* 相同。 

## <a name="examples"></a>範例

只查詢檢視的 *具體化* 部分，而不受上次具體化時的影響。

<!-- csl -->
```
materialized_view("ViewName")
```

只有在過去10分鐘內具體化時，才查詢 *具體化* 元件。 如果具體化元件超過10分鐘，則傳回完整的視圖。 此選項的效能應比查詢具體化元件的效能低。

<!-- csl -->
```
materialized_view("ViewName", 10m)
```

## <a name="notes"></a>注意

* 建立視圖之後，您就可以查詢資料庫中的任何其他資料表，包括參與跨叢集/跨資料庫查詢。
* 具體化視圖不包含在萬用字元等位或搜尋中。
* 查詢 view 的語法是視圖名稱， (像是資料表參考) 。
* 根據內嵌到來源資料表的所有記錄，查詢具體化視圖一律會傳回最新的結果。 此查詢結合了視圖的具體化部分與來源資料表中的所有 unmaterialized 記錄。 如需詳細資訊，請參閱 [幕後](../management/materialized-views/materialized-view-overview.md#how-materialized-views-work) 的詳細資料。
