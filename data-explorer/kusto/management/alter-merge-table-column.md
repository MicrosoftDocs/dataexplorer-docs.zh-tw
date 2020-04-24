---
title: 變更合併表列文件字串 - Azure 資料資源管理員 ( Azure) 的資料資源管理員 。微軟文件
description: 本文介紹 Azure 資料資源管理器中的更改合併表列文檔字串。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 75d298f35a215af5da443f673278e4a252c24cc9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522384"
---
# <a name="alter-merge-table-column-docstrings"></a>變更合併表列文件字串

設置`docstring`指定表的一個或多個欄的屬性。 未**顯式設置的**列將保留此屬性的現有值(如果它們具有)。

有關變更表列文件字串,請參閱[下文](#alter-table-column-docstrings)。

**語法**

`.alter-merge``table`*TableName*表`column-docstring`名`(` *Col1* `:`*文件字串1* =`,` *Col2* `:` *文件string2*_...`)`

**範例** 

```
.alter-merge table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```

## <a name="alter-table-column-docstrings"></a>變更表列文件字串

設置`docstring`指定表的一個或多個欄的屬性。 未顯式設定的列將**刪除**此屬性。

**語法**

`.alter``table`*TableName*表`column-docstring`名`(` *Col1* `:`*文件字串1* =`,` *Col2* `:` *文件string2*_...`)`

**範例** 

```
.alter table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```