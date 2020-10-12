---
title: Kusto Explorer 程式碼重構-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 Kusto Explorer 程式碼重構。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/05/2019
ms.openlocfilehash: 959cf8d25b20d459b48a0c8f1968541b50917a9d
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2020
ms.locfileid: "91942228"
---
# <a name="kusto-explorer-code-refactoring"></a>Kusto Explorer 程式碼重構

類似于其他 Ide，Kusto 提供數個功能來 KQL 查詢編輯和重構。

## <a name="rename-variable-or-column-name"></a>重新命名變數或資料行名稱

`Ctrl` + `R` `Ctrl` + `R` 在 [查詢編輯器] 視窗中按一下，可讓您重新命名目前選取的符號。

請參閱下列可示範體驗的快照：

![在 [查詢編輯器] 視窗中顯示要重新命名之變數的動畫 GIF。有三個專案同時以新名稱取代。](./Images/KustoTools-KustoExplorer/ke-refactor-rename.gif "重構-重新命名")

## <a name="extract-scalars-as-let-expressions"></a>將純量形式的 `let` 運算式解壓縮

您可以按一下，將目前選取的常值升級為 `let` 運算式 `Alt` + `Ctrl` + `M` 。 

![動畫 GIF。查詢編輯器指標會在常值運算式上開始。接著會出現一個 let 語句，將該常值設定為新的變數。](./Images/KustoTools-KustoExplorer/ke-extract-as-let-literal.gif "依原樣解壓縮-常值")

## <a name="extract-tabular-statements-as-let-expressions"></a>將表格式語句解壓縮為 `let` 運算式

您也可以選取文字，然後按一下，將表格式運算式升級為 `let` 語句 `Alt` + `Ctrl` + `M` 。 

![動畫 GIF。在 [查詢編輯器] 中選取了表格式運算式。接著會出現 let 語句，將該表格式運算式設定為新的變數。](./Images/KustoTools-KustoExplorer/ke-extract-as-let-tabular.gif "依表格式的解壓縮")
