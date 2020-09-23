---
title: . 建立-合併資料表-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中建立合併資料表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/05/2020
ms.openlocfilehash: 19dc7db9e344a516b5c92917dccbf8362b1ca858
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2020
ms.locfileid: "91102887"
---
# <a name="create-merge-table"></a>.create-merge table

建立新的資料表，或擴充現有的資料表。 

命令必須在特定資料庫的內容中執行。 

需要 [資料庫使用者](../management/access-control/role-based-authorization.md)權力。

## <a name="syntax"></a>語法

`.create-merge``table` *TableName* ( [columnName： columnType]，... ) [ `with` `(` [ `docstring` `=` *檔*] [ `,` `folder` `=` *資料夾資料夾*] `)` ]

如果資料表不存在，就會與 ". create table" 命令完全相同。

如果資料表 T 存在，而且您傳送 ". create-merge table T (<columns specification>) " 命令，則：

* 先前未存在於 T 中的任何資料行 <columns specification> 都會加入至 t 架構的結尾。
* T 中不是 in 的任何資料行都不 <columns specification> 會從 t 移除。
* <columns specification>存在於 T 中的任何資料行，但使用不同的資料類型，將會導致命令失敗。

## <a name="see-also"></a>另請參閱

* [.create-merge tables](create-merge-tables-command.md)
* [. 建立資料表](create-table-command.md)
