---
title: 重新命名欄 - Azure 資料資源管理員 :微軟文件
description: 本文介紹 Azure 資料資源管理器中的重命名列。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 02e692e01bb8eb0abd49c9673b000b722734db90
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520497"
---
# <a name="rename-column"></a>重新命名欄

更改現有表列的名稱。
要變更多個欄的名稱,請參閱[下面的](#rename-columns)。

**語法**

`.rename``column` 【*資料庫名稱*`.`】*表名*`.`*欄現有名稱*`to`*欄位*

其中*資料庫名稱*,*表名*,*欄現有名稱*,*並*使用[識別子命名規則](../query/schema-entities/entity-names.md)。

## <a name="rename-columns"></a>重新命名資料行

更改同一表中多個現有列的名稱。

**語法**

`.rename``columns` *Col1* `=` =*資料庫名稱*`.`[*表名*`.` *Col2*] `,` ...

該命令可用於交換兩列的名稱(每個列都重命名為另一列的名稱)。