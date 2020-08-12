---
title: 顯示連續資料匯出成品-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中顯示連續資料匯出成品。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: 68b33ffc46df1e3bc4f63f8f7e9c86786116308b
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149346"
---
# <a name="show-continuous-export-artifacts"></a>顯示連續匯出成品

傳回所有執行中由連續匯出匯出的所有成品。 依據命令中的 Timestamp 資料行來篩選結果，只查看感興趣的記錄。 匯出成品的歷程記錄會保留14天。 

## <a name="syntax"></a>語法

`.show``continuous-export` *ContinuousExportName*`exported-artifacts`

## <a name="properties"></a>屬性

| 屬性             | 類型   | 描述                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 連續匯出的名稱。 |

## <a name="output"></a>輸出

| 輸出參數  | 類型     | 描述                            |
|-------------------|----------|----------------------------------------|
| 時間戳記         | Datetime | 連續匯出執行的時間戳記 |
| ExternalTableName | String   | 外部資料表的名稱             |
| Path              | String   | 輸出路徑                            |
| NumRecords        | long     | 匯出至路徑的記錄數目     |

## <a name="example"></a>範例

```kusto
.show continuous-export MyExport exported-artifacts | where Timestamp > ago(1h)
```

| 時間戳記                   | ExternalTableName | Path             | NumRecords | SizeInBytes |
|-----------------------------|-------------------|------------------|------------|-------------|
| 2018-12-20 07：31：30.2634216 | ExternalBlob      | `http://storageaccount.blob.core.windows.net/container1/1_6ca073fd4c8740ec9a2f574eaa98f579.csv` | 10                          | 1024              |
