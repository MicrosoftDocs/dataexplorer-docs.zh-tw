---
title: external_table （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 external_table （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: 4a3a1150996000742f5065df0eddc385074eaa48
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348083"
---
# <a name="external_table"></a>external_table()

依名稱參考外部資料表。

```kusto
external_table('StormEvent')
```

## <a name="syntax"></a>語法

`external_table``(` *TableName* [ `,` *MappingName* ]`)`

## <a name="arguments"></a>引數

* *TableName*：所查詢之外部資料表的名稱。
  必須是參考類型的外部資料表或的字串常 `blob` 值 `adl` 。 <!-- TODO: Document data formats supported -->

* *MappingName*：對應物件的選擇性名稱，其會將實際（外部）資料分區中的欄位對應至此函式的資料行輸出。

**備註**

如需外部資料表的詳細資訊，請參閱[外部資料表](schema-entities/externaltables.md)。

另請參閱[管理外部資料表的命令](../management/externaltables.md)。

`external_table`函數與[資料表](tablefunction.md)函數具有類似的限制。