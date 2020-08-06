---
title: 'external_table ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 external_table ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: 13b244eb151d140e3626412188ac9bc9de242cc6
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87802973"
---
# <a name="external_table"></a>external_table()

依名稱參考外部資料表。

```kusto
external_table('StormEvent')
```

> [!NOTE]
> * `external_table`函數與[資料表](tablefunction.md)函數具有類似的限制。
> * [外部資料表](schema-entities/externaltables.md)
> * [用於管理外部資料表的命令](../management/externaltables.md)

## <a name="syntax"></a>語法

`external_table``(` *TableName* [ `,` *MappingName* ]`)`

## <a name="arguments"></a>引數

* *TableName*：所查詢之外部資料表的名稱。
  必須是參考類型的外部資料表或的字串常 `blob` 值 `adl` 。 <!-- TODO: Document data formats supported -->

* *MappingName*：對應物件的選擇性名稱，它會將實際 (外部) 資料分區中的欄位對應到此函式的資料行輸出。
