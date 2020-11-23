---
title: 函數程式庫-Azure 資料總管
description: 本文說明擴充 Azure 資料總管功能的使用者定義函數。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 552d55cdf3e40a4138b6521ffa2afdd85eeab68b
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324613"
---
# <a name="functions-library"></a>函式程式庫

下列文章包含 [ (使用者定義函數) 的 UDF ](../query/functions/user-defined-functions.md)分類清單。

文章中提供了使用者定義函數程式碼。  它可用於內嵌在查詢中的 let 語句，或可使用 [. create function](../management/create-function.md)保存在資料庫中。

## <a name="machine-learning-functions"></a>機器學習函式

|函數名稱     |說明                                          |
|-------------------------|--------------------------------------------------------|
|[kmeans_fl()](kmeans-fl.md)|使用 k 表示演算法將叢集化。 |
|[predict_fl()](predict-fl.md)|使用現有的定型機器學習模型進行預測。 |
|[predict_onnx_fl()](predict-onnx-fl.md)| 使用 ONNX 格式的現有定型機器學習模型來預測。 |

## <a name="series-processing-functions"></a>數列處理函數

|函數名稱     |說明                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_fl()](quantize-fl.md)|量化計量資料行。 |
|[series_dot_product_fl()](series-dot-product-fl.md)|計算兩個數值向量的點乘積。 |
|[series_exp_smoothing_fl ( # B1 ](series-exp-smoothing-fl.md)|在數列上套用基本指數平滑篩選。 |
|[series_fit_poly_fl()](series-fit-poly-fl.md)|使用迴歸分析將多項式納入數列。 |
|[series_moving_avg_fl()](series-moving-avg-fl.md)|在數列上套用移動平均篩選。 |
|[series_rolling_fl()](series-rolling-fl.md)|在數列上套用滾動彙總函式。 |
