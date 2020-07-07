---
title: 。建立-合併資料表-Azure 資料總管
description: 本文說明 Azure 資料總管中的 create-merge 資料表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/05/2020
ms.openlocfilehash: afe5011717fd77d654eaf6c2b70e9ffbdea87128
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967618"
---
# <a name="create-merge-table"></a>。建立-合併資料表

建立新的資料表或擴充現有的資料表。 

此命令必須在特定資料庫的內容中執行。 

需要[資料庫使用者](../management/access-control/role-based-authorization.md)權力。

**語法**

`.create-merge``table` *TableName* （[columnName： columnType]，...） [ `with` `(` [ `docstring` `=` *檔*] [ `,` `folder` `=` *資料夾*集] `)` ]

如果資料表不存在，則函式會與 ". create table" 命令完全相同。

如果資料表 T 存在，而且您傳送了 ". create-merge table T （ <columns specification> ）" 命令，則：

* <columns specification>先前不存在於 t 中的任何資料行都會加入至 t 架構的結尾。
* 不在 T 中的任何資料行都不 <columns specification> 會從 t 移除。
* 中的任何 <columns specification> 資料行都存在於 T 中，但使用不同的資料類型會導致命令失敗。

**另請參閱**

* [.create-merge tables](create-merge-tables-command.md)
* [.create 資料表](create-table-command.md)
