---
title: parse_path() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 parse_path()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 2267efb4e47a6d8e45733ad48dd9f7f4019c1fa8
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744672"
---
# <a name="parse_path"></a>parse_path()

分析檔路徑`string`並返回包含路徑[`dynamic`](./scalar-data-types/dynamic.md)以下 部分的物件:方案、根路徑、目錄路徑、目錄名稱、檔名、副檔名、備用數據流名稱。
除了具有兩種類型的斜杠的簡單路徑外,還支援具有架構(例如"file://...")、共用路徑(例如"\\共用驅動器\使用者...")、長路徑(例如"_C:...")、\\備用數據流(例如"file1.exe:file2.exe")的路徑。

**語法**

`parse_path(`*路徑*`)`

**引數**

* *路徑*:表示檔案路徑的字串。

**傳回**

如上所述灌輸路徑元件`dynamic`的類型物件。

**範例**

<!-- csl: https://help.kusto.windows.net/Samples -->

```kusto
datatable(p:string) 
[
    @"C:\temp\file.txt",
    @"temp\file.txt",
    "file://C:/temp/file.txt:some.exe",
    @"\\shared\users\temp\file.txt.gz",
    "/usr/lib/temp/file.txt"
]
| extend path_parts = parse_path(p)

```

|p|path_parts
|---|---
|C:\temp_file.txt|{"方案":"","RootPath":"C":""目錄路徑":"C:temp","\\目錄名稱":"temp","檔名":"file.txt","擴展":"txt","備用數據流名稱":""""
|暫存檔.txt|{"方案":"","RootPath":"","目錄路徑":"temp","目錄名稱":"temp","檔名":"file.txt","擴展":"txt","備用數據流名稱":""""
|file://C:/temp/file.txt:some.exe|{"方案":"檔","RootPath":"C","目錄路徑":"C:/temp","目錄名稱":"temp","檔名":"file.txt","擴展":"txt","備用數據流名稱":"一些.exe"]
|\\分享\使用者\temp_file.txt.gz|{"方案":"","RootPath":"","目錄路徑":"\\\\\\共享\\使用者 臨時","目錄名稱":"臨時","檔名":"file.txt.gz","擴展":"gz","備用數據流名稱":""""
|/usr/lib/臨時/檔.txt|{"方案":"","RootPath":"","目錄路徑":"/usr/lib/temp","目錄名稱":"臨時","檔名稱":"file.txt","擴展":"txt","備用數據流名稱":""""
