---
title: 分面運算符 - Azure 資料資源管理員 |微軟文件
description: 本文介紹了 Azure 數據資源管理器中的分面運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0b6c87fc044e0f00f77e28e85d89757a69835164
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515312"
---
# <a name="facet-operator"></a>facet 運算子

返回一組表,每個指定列返回一個表。
每個表指定其列獲取的值的清單。
可以使用`with`子句創建其他表。

**語法**

*T* `| facet by` *欄位*名稱 =`, ` ...]•`with (` *過濾器管*`)`

**引數**

* *欄位名稱:* 要匯總為輸出表的輸入中的列的名稱。
* *過濾器管:* 應用於輸入表以生成其中一個輸出的查詢表達式。

**傳回**

多個表:一個用於`with`子句,一個用於每列。

**範例**

```kusto
MyTable 
| facet by city, eventType 
    with (where timestamp > ago(7d) | take 1000)
```