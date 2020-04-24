---
title: 外部資料運算子 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的外部數據運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 0a4a151d1fd052e27e4daf76539ef6dbd9d94c83
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515465"
---
# <a name="externaldata-operator"></a>externaldata 運算子

操作員`externaldata`返回其架構在查詢本身中定義的表,其數據從外部存儲專案(如 Azure Blob 存儲中的 Blob)讀取。

> [!NOTE]
> 此運算符沒有管道輸入。

**語法**

`externaldata``(`*欄位*`:`*型態*`,`= ...]`)``[`儲存連線字串 = Prop1 值 1 = ...] `]` `with` `(` `=` * * `,` * * * *`)`]

**引數**

* *欄位*名稱,*欄型態*: 定義表的架構.
  語法與 在[.create 表](../management/create-table-command.md)中定義表時使用的語法相同。

* *儲存連線字串*:[儲存連線字串](../api/connection-strings/storage.md)描述儲存要傳回資料的儲存專案。

* *Prop1,Value1*,...:描述如何解釋從存儲檢索的數據的其他屬性,如[在引入屬性](../management/data-ingestion/index.md)下列*Value1*出。
    * 目前支援的屬性:`format``ignoreFirstRecord`與 。
    * 支援的資料格式:支援任何[引入資料格式](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats),`json``parquet``avro`包括`csv``tsv`、、、、、、

**傳回**

運算符`externaldata`返回給定架構的數據表,其數據從儲存連接字串(Int)指示的指定儲存工件進行分析。

**範例**

下面的範例展示如何查找表中的所有記錄,其`UserID`列落入外部Blob中保留(每行一個)的已知一組指示。
由於該集由查詢間接引用,因此可能非常大。

```
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt"
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```