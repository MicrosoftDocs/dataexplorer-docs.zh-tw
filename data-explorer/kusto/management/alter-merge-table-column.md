---
title: alter-merge 資料表資料行-docstrings-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 alter merge 資料表資料行 docstrings。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9f71a46e6c747b7f9d9f6a3ba2d2f8a308635128
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967208"
---
# <a name="alter-merge-table-column-docstrings"></a>.alter-merge table column-docstrings

設定 `docstring` 指定之資料表的一個或多個資料行的屬性。 未明確設定的資料行會**保留**其現有的此屬性值（如果有的話）。

如需 alter table 資料行-docstring，請參閱[下面](#alter-table-column-docstrings)的。

**語法**

`.alter-merge``table` *TableName* `column-docstring` TableName `(`*Col1* `:`*Docstring1* [ `,` *Col2* `:` *Docstring2*] .。。`)`

**範例** 

```kusto
.alter-merge table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```

## <a name="alter-table-column-docstrings"></a>alter table 資料行-docstrings

設定 `docstring` 指定之資料表的一個或多個資料行的屬性。 未明確設定的資料行將會**移除**此屬性。

**語法**

`.alter``table` *TableName* `column-docstring` TableName `(`*Col1* `:`*Docstring1* [ `,` *Col2* `:` *Docstring2*] .。。`)`

**範例** 

```kusto
.alter table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```
