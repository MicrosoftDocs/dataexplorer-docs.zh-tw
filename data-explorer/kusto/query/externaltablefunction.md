---
title: external_table() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 external_table()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: 9fd03fb3c8452702c3db27a5e0466e8608c04eb9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515448"
---
# <a name="external_table"></a>external_table()

按名稱引用外部表。

```kusto
external_table('StormEvent')
```

**語法**

`external_table``(`*表名*`,`[*映射名稱*】`)`

**引數**

* *表名稱*:要查詢的外部表的名稱。
  必須是引用類型`blob``adl`或的外部表的字串文本。 <!-- TODO: Document data formats supported -->

* *映射名稱*:映射物件的可選名稱,用於將實際(外部)數據分片中的欄位映射到此函數輸出的列。

**注意事項**

有關外部表的詳細資訊,請參閱[外部表](schema-entities/externaltables.md)。

另請參考[使用管理外部表的指令](../management/externaltables.md)。

該`external_table`函數具有與[表](tablefunction.md)函數類似的限制。