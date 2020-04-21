---
title: 函式庫斯托資源管理員代碼導覽 ─ Azure 資料資源管理員 :微軟文件
description: 本文介紹了 Azure 資料資源管理器中的 Kusto 資源管理器代碼導航。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/31/2020
ms.openlocfilehash: c81ea5360013b779717e87164a71baf5ac4ae6c1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524101"
---
# <a name="kusto-explorer-code-navigation"></a>函式庫斯托資源管理員碼導航

Kusto.Explorer 提供了幾個功能,便於使用查詢符號信息進行代碼導航。

## <a name="go-to-symbol-definition"></a>跳到符號定義

您可以使用`F12``Alt`+`Home`或短切導航到當前符號的定義。

## <a name="list-all-references-of-a-symbol"></a>列出符號的引照

`Ctrl`使用`F12`短切獲取當前符號的所有+參考。

![alt 文字](./Images/KustoTools-KustoExplorer/ke-codenav-refernces.gif "代碼瀏覽參考")