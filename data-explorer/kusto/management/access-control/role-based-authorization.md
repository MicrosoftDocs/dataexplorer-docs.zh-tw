---
title: Kusto 中以角色為基礎的授權-Azure 資料總管
description: 本文說明 Azure 資料總管的 Kusto 中以角色為基礎的授權。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/14/2020
ms.openlocfilehash: 8e961d389b365d77c7dddd557a28a158add2e3f3
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329073"
---
# <a name="role-based-authorization-in-kusto"></a>Kusto 中以角色為基礎的授權

「授權」（ **Authorization** ）是允許或禁止安全性主體許可權來執行動作的程式。
Kusto 會使用以**角色為基礎的存取控制**模型，在此模式下，已驗證的主體會對應至**角色**，並根據其被指派的角色取得存取權。

**Kusto Engine**服務具有下列角色：

|角色                       |權限                                                                                                                                                  |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
|所有資料庫管理員        |可以在任何資料庫的範圍內執行任何動作。 可以顯示和改變特定叢集層級原則                                                               |
|資料庫管理             |可以在特定資料庫的範圍內執行任何動作                                                                                                         |
|資料庫使用者              |可以讀取資料庫的所有資料和中繼資料。 此外，也可以建立資料表，並成為這些資料表的資料表管理員，並在資料庫中建立函數。|
|所有資料庫檢視器       |可以讀取任何資料庫的所有資料和中繼資料                                                                                                               |
|資料庫檢視器            |可以讀取特定資料庫的所有資料和中繼資料                                                                                                       |
|資料庫擷取器          |可以將資料內嵌至資料庫中的所有現有資料表，但無法查詢資料                                                                             |
|資料庫不受限制檢視器|可以查詢資料庫中已啟用[RestrictedViewAccess 原則](../restrictedviewaccess-policy.md)的所有資料表                                |
|資料庫監視器           |可以 `.show` 在資料庫及其子實體的內容中執行命令                                                                           |
|函數管理員             |可以改變 function、delete 函式或授與系統管理員許可權給另一個主體                                                                         |
|資料表系統管理員                |可以在特定資料表的範圍內執行任何動作                                                                                                           |
|資料表擷取器             |可以在特定資料表的範圍內內嵌資料，但無法查詢資料                                                                                 |
