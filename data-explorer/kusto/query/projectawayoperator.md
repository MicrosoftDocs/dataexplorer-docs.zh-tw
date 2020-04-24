---
title: 項目離開運算子 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的項目離開運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 38ec57e9659458ef34117e4a380c756310db218b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510943"
---
# <a name="project-away-operator"></a>project-away 運算子

選擇要從輸出中排除的輸入欄位

```kusto
T | project-away price, quantity, zz*
```

結果中的列的順序由它們在表中的原始順序決定。 只刪除指定為參數的欄。 結果中包括其他列。  (另請參閱 `project`)。

**語法**

*T* `| project-away` *欄位名稱或模式*[`,` ...

**引數**

* *T*: 輸入表
* *欄名稱或圖案:* 要從輸出中刪除的列或列通配符模式的名稱。

**傳回**

包含未命名為參數的列的表。 包含與輸入表相同的行數。

**技巧**

* 如果[`project-rename`](projectrenameoperator.md)打算重新命名列,請使用。
* 如果[`project-reorder`](projectreorderoperator.md)意圖是重新排序列,請使用。

* 可以`project-away`顯示原始表中存在或作為查詢的一部分計算的任何列。


**範例**

輸入資料表 `T` 有三個類型為 `long` 的資料行：`A`、`B` 和 `C`。

```kusto
datatable(A:long, B:long, C:long) [1, 2, 3]
| project-away C    // Removes column C from the output
```

|A|B|
|---|---|
|1|2|

刪除以"a"開頭的欄。

```kusto
print  a2='a2', b = 'b', a3='a3', a1='a1'
|  project-away a* 
```

|b|
|---|
|b|

