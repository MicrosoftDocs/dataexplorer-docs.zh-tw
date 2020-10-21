---
title: 'external_table ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 external_table。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: 5ec069979d41a7c750c140ad84ef0db4ba5638a4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245060"
---
# <a name="external_table"></a>external_table()

依名稱參考 [外部資料表](schema-entities/externaltables.md) 。

```kusto
external_table('StormEvent')
```

> [!NOTE]
> * `external_table`函數與[資料表](tablefunction.md)函數具有類似的限制。
> * 標準 [查詢限制](../concepts/querylimits.md) 也適用于外部資料表查詢。

## <a name="syntax"></a>語法

`external_table``(` *TableName* [ `,` *MappingName* ]`)`

## <a name="arguments"></a>引數

* *TableName*：要查詢的外部資料表名稱。
  必須是參考外部資料表（或）的字串常 `blob` 值 `adl` `sql` 。

* *MappingName*：對應物件的選擇性名稱，此名稱會將實際 (external) 資料分區中的欄位，對應至此函數輸出的資料行。

## <a name="next-steps"></a>後續步驟

* [外部資料表一般控制項命令](../management/externaltables.md)
* [建立和改變 Azure 儲存體或 Azure Data Lake 中的外部資料表](../management/external-tables-azurestorage-azuredatalake.md)
* [建立和改變外部 SQL 資料表](../management/external-sql-tables.md)
