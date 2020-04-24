---
title: 重複() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的重複()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0fd944112a64e7400e38c627b7b0651bb6fccd54
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510416"
---
# <a name="repeat"></a>repeat()

生成包含一系列相等值的動態數位。

**語法**

`repeat(`*值*`,`*計數*`)` 

**引數**

* *值*:生成陣列中元素的值。 *值*的類型可以是布爾、整數、長、真、日期或時間跨度。   
* *計數*:結果陣列中元素的計數。 *計數*必須為整數。
如果*計數*等於零,則返回空陣組。
如果*計數*小於零,則返回空值。 

**範例**

下列範例會傳回 `[1, 1, 1]`：

```kusto
T | extend r = repeat(1, 3)
```