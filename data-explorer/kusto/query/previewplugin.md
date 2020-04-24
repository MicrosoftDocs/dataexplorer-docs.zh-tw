---
title: 預覽外掛程式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹了 Azure 數據資源管理器中的預覽外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 18bda0a4348d0c0eb2776bf124c57397f318a989
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510977"
---
# <a name="preview-plugin"></a>預覽外掛程式

返回具有最多來自輸入記錄集中的指定行數的表,以及輸入記錄集中的記錄總數。

```kusto
T | evaluate preview(50)
```

**語法**

`T` `|` `evaluate` `preview(` *NumberOfRows* `)`

**傳回**

外掛`preview`程式 傳回兩個結果表:
* 具有最多指定行數的表。
  例如,上面的範例查詢等效於執行`T | take 50`。
* 具有單個行/列的表,保存輸入記錄集中的記錄數。
  例如,上面的範例查詢等效於執行`T | count`。

**技巧**

如果`evaluate`前面有包含複雜篩選器的表格來源,或是參考大多數源表列的篩選器,則更喜歡使用函[`materialize`](materializefunction.md)式 。 例如：

```kusto
let MaterializedT = materialize(T);
MaterializedT | evaluate preview(50)
```