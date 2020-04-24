---
title: guid 資料類型 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 guid 數據類型。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/15/2020
ms.openlocfilehash: 382b2da0ab791cf3e2fea0e1c785ee42634aab07
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509855"
---
# <a name="the-guid-data-type"></a>guid 資料類型

`guid` (`uuid` `uniqueid`、 ) 數據類型表示 128 位元全域唯一值。

> [!WARNING]
> 在撰寫本文時，`guid`類型的支援不完整。
> 主要差距是缺少此類型的列上的索引,從而影響對此類類型進行謂詞的查詢的性能。
> 我們強烈建議小組改用 `string` 類型的值。

## <a name="guid-literals"></a>吉德文字

要表示型態`guid`的文字,請使用以下格式:

```kusto
guid(74be27de-1e4e-49d9-b579-fe0b331d3642)
```

特殊表單`guid(null)`表示[null 值](null-values.md)。