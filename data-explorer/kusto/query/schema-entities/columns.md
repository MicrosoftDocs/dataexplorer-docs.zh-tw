---
title: 列 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的列。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 17406eb94f8e29f4b8ab87653ab41df737fa15ef
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509481"
---
# <a name="columns"></a>資料行

Kusto 中的每個[表](tables.md)和每個表格資料流都是列和行的矩形網格。 表中的每個欄位都有一個名稱和特定的[標量資料型態](../scalar-data-types/index.md)。 對表或表格資料串流的列進行排序,因此列在表的列集合中也有特定位置。

**注意事項**  

* 列名稱區分大小寫。
* 列名稱遵循[實體名稱](./entity-names.md)的規則。
* 每個表的列的最大限制為 10,000。

在查詢中,列通常僅按名稱引用。 它們只能出現在表達式中,而表達式顯示的查詢運算符決定了表或表格數據流,因此不必進一步限定列的名稱範圍。 例如,在下面的查詢中,我們有一個未命名的表格資料串流(透過單列[的資料表運算符定義](../datatableoperator.md)`c`。 然後,表格數據流由該列的值的謂詞篩選,生成具有相同列但行較少的新的未命名的表格數據流。 [然後,as 運算符](../asoperator.md)命名表格數據流,其值將作為查詢結果返回。
請注意列如何`c`按名稱引用,而無需引用其容器(事實上,該容器沒有名稱):

```kusto
datatable (c:int) [int(-1), 0, 1, 2, 3]
| where c*c >= 2
| as Result
```

> 在關係資料庫世界中很常見,行有時稱為**記錄**,列有時稱為**屬性**。

有關管理列的詳細資訊,請參閱[管理欄位](../../management/columns.md)。