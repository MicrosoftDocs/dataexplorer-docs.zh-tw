---
title: 庫斯特微軟 SQL 伺服器之間的 MS-TDS/T-SQL 差異 - Azure 數據資源管理員 |微軟文件
description: 本文介紹了 Azure 資料資源管理器中的 Kusto Microsoft SQL Server 之間的 MS-TDS/T-SQL 差異。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/04/2019
ms.openlocfilehash: be294053fdd0f95d488f52b547ef7abd0ef7c23c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523421"
---
# <a name="ms-tdst-sql-differences-between-kusto-microsoft-sql-server"></a>庫斯特微軟 SQL 伺服器之間的 MS-TDS/T-SQL 差異

下面是 Kusto 和 SQL Server 實現 T-SQL 之間的主要區別的部分清單。

## <a name="create-insert-drop-alter-statements"></a>建立、插入、丟棄、變更語句

Kusto 不支援透過 MS-TDS 進行架構修改或數據修改,也不支援上述 T-SQL 語句。

## <a name="correlated-sub-queries"></a>相關的子查詢

Kusto 不`SELECT`支援 中的相關子`WHERE`查詢`JOIN`, 和 子句。

## <a name="top-flavors"></a>頂級口味

Kusto`WITH TIES`忽略 查詢並將其計算`TOP`為常規 查詢。
函式庫斯特`PERCENT`不支援 。

## <a name="cursors"></a>資料指標

Kusto 不支援 SQL 遊標。

## <a name="flow-control"></a>流量控制

Kusto 不支援流控制語句,但少數有限情況除外,`IF``THEN``ELSE`例如`THEN``ELSE`具有和批處理的相同架構的子句。

## <a name="data-types"></a>資料類型

根據查詢的不同,返回的數據的類型可能與 SQL Server 中的數據類型不同。
此處的一個明顯示例是像`TINYINT``SMALLINT`和 在 Kusto 中沒有等效類型。 因此,`BYTE`期望類型值`INT16`或`INT32`可能`INT64`獲取或值的用戶端。

## <a name="column-order-in-results"></a>結果欄順序

當`SELECT`語句中使用 asterix 時,每個結果集中的列順序可能因 Kusto 和 SQL Server 而異。 在這些情況下,使用列名稱的用戶端效果會更好。
如果`SELECT`語句中沒有 asterix 字元,則將保留列字元。

## <a name="columns-name-in-results"></a>結果中的欄名稱

在 T-SQL 中,多個列可能具有相同的名稱。 庫托不允許這樣做。
如果名稱發生衝突,列的名稱在 Kusto 中可能不同。
但是,原始名稱將被保留,至少對於其中一列。

## <a name="any-all-and-exists-predicates"></a>任何、全部和存在謂詞

Kusto 不支援`ANY`謂`ALL`詞`EXISTS`, 與 。

## <a name="recursive-ctes"></a>遞迴 CTE

Kusto 不支援遞歸通用表表達式。

## <a name="dynamic-sql"></a>動態 SQL

Kusto 不支援動態 SQL 語句(查詢生成的 SQL 文本的內聯執行)。

## <a name="within-group"></a>WITHIN GROUP

庫斯特托不支持`WITHIN GROUP`條款。

## <a name="truncate-function"></a>TrunCATE 函數

Kusto 中的 TRUNCATE 函數 (ODBC) 的工作方式與 ROUND 類似,這意味著結果將是最接近的值,而不是在 SQL 中傳回的下值。