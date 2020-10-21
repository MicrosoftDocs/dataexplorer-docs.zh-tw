---
title: 'parse_path ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 parse_path。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 07224c01e1bd226575aff6555ca7cd0a82c049b2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251680"
---
# <a name="parse_path"></a>parse_path()

剖析檔案路徑 `string` ，並傳回 [`dynamic`](./scalar-data-types/dynamic.md) 包含下列路徑部分的物件：
* 配置
* RootPath
* DirectoryPath
* DirectoryName
* FileName
* 副檔名
* AlternateDataStreamName

除了這兩種斜線的簡單路徑之外，函式還支援路徑與：
* 結構描述。 例如，"file://..."
* 共用路徑。 例如，" \\ shareddrive\users..."
* 完整路徑。 例如，" \\ ？ \c： ..." "
* 替代資料流。 例如，"file1.exe:file2.exe"

## <a name="syntax"></a>語法

`parse_path(`*path*`)`

## <a name="arguments"></a>引數

* *路徑*：代表檔案路徑的字串。

## <a name="returns"></a>傳回

型別的物件 `dynamic` ，其中包含如上所列的路徑元件。

## <a name="example"></a>範例

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
|\\shared\users\temp\file.txt. gz|{"配置"： ""，"RootPath"： ""，"DirectoryPath"： " \\ \\共用 \\ 使用者 \\ temp "，" DirectoryName "：" temp "，" Filename "：" file.txt. gz "，" Extension "：" gz "，" AlternateDataStreamName "：" "}
|/usr/lib/temp/file.txt|{"配置"： ""，"RootPath"： ""，"DirectoryPath"： "/usr/lib/temp"，"DirectoryName"： "temp"，"Filename"： "file.txt"，"Extension"： "txt"，"AlternateDataStreamName"： ""}
