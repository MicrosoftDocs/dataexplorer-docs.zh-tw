---
title: 不支援的標量資料類型 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理員中不受支援的標量數據類型。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 76a548abdf05cec57e4d0558e5d98ab0f7cbdc3e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509583"
---
# <a name="unsupported-scalar-data-types"></a>不支援的標量資料型態

Kusto 中認為所有未記錄**的標量數據類型**都不受支援。

不支援的類型包括以下內容。 以前支援某些類型:

| 類型       | 其他名稱   | 對等的 .NET 類型              | 儲存體類型 (內部名稱)|
| ---------- | -------------------- | --------------------------------- | ----------------------------|
| `float`    |                      | `System.Single`                   | `R32`                       |
| `int16`    |                      | `System.Int16`                    | `I16`                       |
| `uint16`   |                      | `System.UInt16`                   | `UI16`                      |
| `uint32`   | `uint`               | `System.UInt32`                   | `UI32`                      |
| `uint64`   | `ulong`              | `System.UInt64`                   | `UI64`                      |
| `uint8`    | `byte`               | `System.Byte`                     | `UI8`                       |