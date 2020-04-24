---
title: 放置欄 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的刪除列。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 56ba5499b27b517b3080ee27ac317aa1e0917edd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521211"
---
# <a name="drop-column"></a>放置欄位

從表中刪除欄位。
要從表格中移除多個欄,請參考[下面的](#drop-table-columns)。

> [!WARNING]
> 此命令不可逆。 刪除欄中的資料都將被刪除。
> 將來要將該列添加回來的命令將無法還原數據。

**語法**

`.drop``column`*表名*`.`*欄位*

## <a name="drop-table-columns"></a>放置表列

從表中刪除多個欄位。

> [!WARNING]
> 此命令不可逆。 刪除的欄中的所有資料都將被刪除。
> 將來要將這些列重新添加的命令將無法還原資料。

**語法**

`.drop``table`*TableName*表`columns`名`(` *Col1* =`,` *Col2*_...`)`