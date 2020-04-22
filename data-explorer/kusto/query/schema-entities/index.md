---
title: 實體 - Azure 資料總管 | Microsoft Docs
description: 本文說明 Azure 資料總管中的實體。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: d31765d72d37b0146cf7ba8a42e02722296bf80e
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663475"
---
# <a name="entities"></a>實體

Kusto 查詢會在附加至 Kusto 叢集的某些 Kusto 資料庫內容中執行。 資料庫中的資料會以資料表的形式排列，查詢可能會參考這些資料表，並在資料表內組織成資料行和資料列的矩形方格。 此外，查詢可能會參考資料庫中的預存函式，這是可重複使用的查詢片段。

* 叢集是保存資料庫的實體。
  叢集沒有名稱，但是可以使用 `cluster()` 特殊函式搭配叢集的 URI 來參考。
  例如，`cluster("https://help.kusto.windows.net")` 是保存 `Samples`資料庫之叢集的參考。

* [資料庫](./databases.md)是保存資料表和預存函式的具名實體會。 所有 Kusto 查詢都會在某個資料庫的內容中執行，而該資料庫的實體可能由查詢參考，而不需具任何資格。 此外，您可以使用 [database() 特殊函式](../databasefunction.md)參考叢集的其他資料庫，或資料庫或其他叢集。 例如， `cluster("https://help.kusto.windows.net").database("Samples")`
  是特定資料庫的通用參考。

* [資料表](./tables.md)是可保存資料的具名實體。 資料表具有一組已排序的資料行，以及零個或多個資料列，每個資料列會針對資料表的每個資料行保存一個資料值。 只有資料表位於查詢內容的資料庫時，才可以依名稱參考，否則會以資料庫參考加以限定。 例如，`cluster("https://help.kusto.windows.net").database("Samples").StormEvents` 是 `Samples` 資料庫中特定資料表的通用參考。
  您也可以使用 [table() 特殊函式](../tablefunction.md)來參考資料表。

* [資料行](./columns.md)是具有[純量資料類型](../scalar-data-types/index.md)的具名實體。
  資料行會在查詢中參考，而該查詢與在參考它們的特定運算子內容中的表格式資料流相關。

* [預存函式](./stored-functions.md)是可讓您重複使用 Kusto 查詢或查詢組件的具名實體。

* [外部資料表](./externaltables.md)是參考儲存在 Kusto 資料庫外部的資料實體。
  外部資料表是用來將資料從 Kusto 匯出到外部儲存體，並在不需要內嵌到 Kusto 的情況下，用來查詢外部資料。