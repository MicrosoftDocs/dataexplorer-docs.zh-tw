---
title: Kusto MS-TDS/T-SQL 與 SQL Server 的差異-Azure 資料總管
description: 本文說明 Azure 資料總管中 Kusto Microsoft SQL Server 之間的毫秒-TDS/T-SQL 差異。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/04/2019
ms.openlocfilehash: c949b5bb3659d82ed586a39b4310495e61934777
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382364"
---
# <a name="ms-tdst-sql-differences-between-kusto-microsoft-sql-server"></a>MS-Kusto Microsoft SQL Server 之間的 TDS/T-sql 差異

以下是 Kusto 和 SQL Server 的 T-sql 執行之間主要差異的部分清單。

## <a name="create-insert-drop-alter-statements"></a>CREATE、INSERT、DROP、ALTER 語句

Kusto 不支援透過 MS TDS 進行架構修改或資料修改，也不支援上述 T-sql 語句。

## <a name="correlated-sub-queries"></a>相互關聯的子查詢

Kusto 不支援 `SELECT` 、和子句中的相互關聯子查詢 `WHERE` `JOIN` 。

## <a name="top-flavors"></a>熱門風格

Kusto 會忽略 `WITH TIES` 並定期評估查詢 `TOP` 。
Kusto 不支援 `PERCENT` 。

## <a name="cursors"></a>資料指標

Kusto 不支援 SQL 資料指標。

## <a name="flow-control"></a>流量控制

Kusto 不支援流量控制語句，但有幾個有限的案例，例如 `IF` `THEN` `ELSE` 具有 `THEN` 和批次相同架構的子句 `ELSE` 。

## <a name="data-types"></a>資料類型

視查詢而定，傳回的資料可能會與 SQL Server 中的類型不同。
這裡有一個明顯的例子，就 `TINYINT` 是 `SMALLINT` 在 Kusto 中沒有對等的類型，例如和。 因此，如果用戶端預期類型為或的值，則 `BYTE` `INT16` 可能會改為取得 `INT32` 或 `INT64` 值。

## <a name="column-order-in-results"></a>結果中的資料行順序

在語句中使用星號時 `SELECT` ，每個結果集中的資料行順序可能會在 Kusto 和 SQL Server 之間有所不同。 使用資料行名稱的用戶端在這些情況下會有更好的效果。
如果語句中沒有星號字元 `SELECT` ，則會保留資料行序數。

## <a name="columns-name-in-results"></a>結果中的資料行名稱

在 T-sql 中，多個資料行的名稱可能相同。 Kusto 中不允許這種情況。
如果名稱發生衝突，Kusto 中的資料行名稱可能會不同。
不過，原始名稱會保留，至少其中一個資料行。

## <a name="any-all-and-exists-predicates"></a>ANY、ALL 和 EXISTS 述詞

Kusto 不支援述詞 `ANY` 、 `ALL` 和 `EXISTS` 。

## <a name="recursive-ctes"></a>遞迴 CTE

Kusto 不支援遞迴通用資料表運算式。

## <a name="dynamic-sql"></a>動態 SQL

Kusto 不支援動態 SQL 語句（由查詢產生的 SQL 腳本的內嵌執行）。

## <a name="within-group"></a>WITHIN GROUP

Kusto 不支援 `WITHIN GROUP` 子句。

## <a name="truncate-function"></a>截斷函式

Kusto 中的截斷函式（ODBC）的運作方式類似于 ROUND，這表示結果會是最接近的值，而不是 SQL 中傳回的較低值。