---
title: 函式 - Azure 資料總管 | Microsoft Docs
description: 本文說明 Azure 資料總管中的函式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.localizationpriority: high
adobe-target: true
ms.openlocfilehash: 234ae530fcd664d8d12418ed85d8394385bebf75
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2021
ms.locfileid: "100359926"
---
# <a name="function-types"></a>函式類型

**Functions** 是可重複使用的查詢或查詢元件。 Kusto 支援數種函式：

* **預存函式**，這是使用者自訂的函式，用於儲存和管理一種資料庫的結構描述實體。
  請參閱[預存函式](../schema-entities/stored-functions.md)。
* **查詢定義的函式**，這是在單一查詢範圍內定義和使用的使用者定義函式。 這類函式的定義是透過 [let 陳述式](../letstatement.md)來完成。
  請參閱[使用者定義的函式](./user-defined-functions.md)。
* **內建函式**，這是硬式編碼的函式 (由 Kusto 定義，使用者無法加以修改)。