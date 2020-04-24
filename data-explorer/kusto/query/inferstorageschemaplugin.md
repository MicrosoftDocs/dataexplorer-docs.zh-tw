---
title: infer_storage_schema外掛程式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的infer_storage_schema外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 6c4543a3b029017067867bb70d913509941c332e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513901"
---
# <a name="infer_storage_schema-plugin"></a>infer_storage_schema外掛程式

此外掛程式推斷外部資料的架構,並將其返回為 CSL 架構字串,可在[創建外部表](../management/externaltables.md#create-or-alter-external-table)時使用。

```kusto
let options = dynamic({
  'StorageContainers': [
    h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
  ],
  'DataFormat': 'parquet',
  'FileExtension': '.parquet'
});
evaluate infer_storage_schema(options)
```

**語法**

`evaluate` `infer_storage_schema(` *選項* `)`

**引數**

單個*Options*參數是一個`dynamic`類型的 常數值,它儲存一個屬性套件,指定請求的屬性:

|名稱                    |必要|描述|
|------------------------|--------|-----------|
|`StorageContainers`|是|數位表示儲存資料的前置伺服器的前置伺服器[storage connection strings](../api/connection-strings/storage.md)|
|`DataFormat`|是|支援[的資料格式](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)之一。|
|`FileExtension`|否|僅掃描以此文件副檔名結尾的檔。 它不是必需的,但指定它可能會加快進程(或消除資料讀取問題)|
|`FileNamePrefix`|否|僅掃描以此前綴開頭的檔。 它不是必需的,但指定它可能會加快進程|
|`Mode`|否|架構推理原則,其中一個`any` `last` `all`: 。 從任何(首次找到)檔、上次寫入檔或所有檔分別推斷數據架構。 預設值是 `last`。|

**傳回**

外掛`infer_storage_schema`程式傳回一個結果表,其中包含一個包含 CSL 架構字串的行/列。

> [!NOTE]
> * 架構推理策略"all"是非常"昂貴的"操作,因為它意味著從找到*的所有*工件中讀取並合併其架構。
> * 由於類型猜測錯誤(或者由於架構合併過程的結果),某些返回的類型可能不是實際類型。 因此,建議在創建外部表之前仔細檢查結果。

**範例**

```kusto
let options = dynamic({
  'StorageContainers': [
    h@'https://storageaccount.blob.core.windows.net/MovileEvents/2015;secretKey'
  ],
  'FileExtension': '.parquet',
  'FileNamePrefix': 'part-',
  'DataFormat': 'parquet'
});
evaluate infer_storage_schema(options)
```

*結果*

|CslSchema|
|---|
|app_id:字串,user_id:長,event_time:日期時間,國家:字串,城市:字串,device_type:字串,device_vendor:字串,ad_network:字串,市場:字串,site_id:字串,site_id:字串,event_type:字串,event_name:字串,有機:字串,days_from_install:int,收入:真實|