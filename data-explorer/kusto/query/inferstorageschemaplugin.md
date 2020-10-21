---
title: infer_storage_schema 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中 infer_storage_schema 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 3c3aa61bdb804d2a1bd6735ea4a22e06e1f1878e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249006"
---
# <a name="infer_storage_schema-plugin"></a>infer_storage_schema 外掛程式

此外掛程式會推斷外部資料的架構，並將其傳回為 CSL 架構字串。 [建立外部資料表](../management/external-tables-azurestorage-azuredatalake.md#create-or-alter-external-table)時，可以使用此字串。

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

## <a name="syntax"></a>語法

`evaluate` `infer_storage_schema(` *選項* `)`

## <a name="arguments"></a>引數

單一 *選項* 引數是類型的常數值 `dynamic` ，可保留屬性包來指定要求的屬性：

|名稱                    |必要|描述|
|------------------------|--------|-----------|
|`StorageContainers`|是|表示儲存的資料成品前置詞 URI 的 [儲存體連接字串](../api/connection-strings/storage.md) 清單|
|`DataFormat`|是|其中一個支援的 [資料格式](../../ingestion-supported-formats.md)。|
|`FileExtension`|否|僅掃描以這個副檔名結尾的檔案。 這並非必要，但指定它可能會加速進程 (或消除資料讀取問題) |
|`FileNamePrefix`|否|只掃描開頭為此前置詞的檔案。 這並非必要，但指定它可能會加速進程|
|`Mode`|否|架構推斷策略，下列其中一個： `any` 、 `last` 、 `all` 。 從) 檔案、最後寫入的檔案，或從所有檔案中找到的任何 (推斷資料架構。 預設值為 `last`。|

## <a name="returns"></a>傳回

外掛程式會傳回 `infer_storage_schema` 單一結果資料表，內含保存 CSL 架構字串的單一資料列/資料行。

> [!NOTE]
> * 儲存體容器 URI 秘密金鑰除了*讀取*之外，還必須具有*清單*的許可權。
> * 架構推斷策略「全部」是一項非常「昂貴」的作業，因為它會從找到的 *所有* 成品中讀取併合並其架構。
> * 某些傳回的型別可能不是因為錯誤的型別猜測 (的結果，或是架構合併處理) 的結果。 這就是為什麼您應該仔細檢查結果，再建立外部資料表。

## <a name="example"></a>範例

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
|app_id： string、user_id： long、event_time： datetime、country： string、city： string、device_type： string、device_vendor： string、ad_network： string、活動： string、site_id： string、event_type： string、event_name： string、有機： string、days_from_install： int、收益： real|
