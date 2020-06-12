---
title: parse_path （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 parse_path （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: ba74b7c1e78d568cc34845d56dc9768f2628192f
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717371"
---
# <a name="parse_path"></a>parse_path()

剖析檔案路徑 `string` 並傳回 [`dynamic`](./scalar-data-types/dynamic.md) 物件，其中包含路徑的下列部分：
* 配置
* RootPath
* DirectoryPath
* DirectoryName
* FileName
* 分機
* AlternateDataStreamName

除了具有這兩種斜線的簡單路徑以外，函式也支援的路徑：
* 結構描述。 例如，"file://..."
* 共用路徑。 例如，" \\ shareddrive\users..."
* 長路徑。 例如，" \\ ？ \c： ..." "
* 替代資料流。 例如，"file1.exe:file2.exe"

**語法**

`parse_path(`*path*`)`

**引數**

* *path*：代表檔案路徑的字串。

**傳回**

類型的物件 `dynamic` ，其中包含如上所列的路徑元件。

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
|C:\temp\file.txt|{"配置"： ""，"RootPath"： "C："，"DirectoryPath"： "C： \\ temp"，"DirectoryName"： "temp"，"Filename"： "file.txt"，"Extension"： "txt"，"AlternateDataStreamName"： ""}
|temp\file.txt|{"配置"： ""，"RootPath"： ""，"DirectoryPath"： "temp"，"DirectoryName"： "temp"，"Filename"： "file.txt"，"Extension"： "txt"，"AlternateDataStreamName"： ""}
|file://C:/temp/file.txt:some.exe|{"配置"： "file"，"RootPath"： "C："，"DirectoryPath"： "C：/temp"，"DirectoryName"： "temp"，"Filename"： "file.txt"，"Extension"： "txt"，"AlternateDataStreamName"： "some.exe"}
|\\shared\users\temp\file.txt。 gz|{"配置"： ""，"RootPath"： ""，"DirectoryPath"： " \\ \\共用 \\ 使用者 \\ temp "，" DirectoryName "：" temp "，" Filename "：" file.txt. gz "，" Extension "：" gz "，" AlternateDataStreamName "：" "}
|/usr/lib/temp/file.txt|{"配置"： ""，"RootPath"： ""，"DirectoryPath"： "/usr/lib/temp"，"DirectoryName"： "temp"，"Filename"： "file.txt"，"Extension"： "txt"，"AlternateDataStreamName"： ""}
