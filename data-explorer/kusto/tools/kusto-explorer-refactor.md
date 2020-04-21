---
title: 庫斯托資源管理員代碼重構 - Azure 資料資源管理員 |微軟文件
description: 本文介紹了 Azure 資料資源管理器中的 Kusto 資源管理器代碼重構。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/05/2019
ms.openlocfilehash: 0a89cf9c648fc4811d56c22012cdb25d5505eb4c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523982"
---
# <a name="kusto-explorer-code-refactoring"></a>函式庫斯托資源管理員碼重構

與其他 IDE 類似,Kusto.Explorer 為 KQL 查詢編輯和重構提供了幾個功能。

## <a name="rename-variable-or-column-name"></a>重新命名變數或欄名稱

`Ctrl``Ctrl`+`R`按下「 查詢編輯器」 視窗中的 將允許您重新命名目前的符號。 + `R`

請參閱下面演示體驗的快照:

![alt 文字](./Images/KustoTools-KustoExplorer/ke-refactor-rename.gif "重構-重新命名")

## <a name="extract-scalars-as-let-expressions"></a>擷取標量`let`為 表示式

您可以`Alt`+通過`Ctrl``let`+按一下 將目前選擇`M`的文字提升為 運算式。 

![alt 文字](./Images/KustoTools-KustoExplorer/ke-extract-as-let-literal.gif "擷取為讓文字")

## <a name="extract-tabular-statements-as-let-expressions"></a>擷取表式敘述`let`為 表示式

還可以通過選擇`let`表格表達式的文本然後按`Alt`+`Ctrl`+`M`一下 將其提升為語句。 

![alt 文字](./Images/KustoTools-KustoExplorer/ke-extract-as-let-tabular.gif "擷取為出租表面")
