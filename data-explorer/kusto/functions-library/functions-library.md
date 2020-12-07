---
title: 函數程式庫-Azure 資料總管
description: 本文說明擴充 Azure 資料總管功能的使用者定義函數。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: fb08edf4105c44a6be96cf6b2f314cf25887e69a
ms.sourcegitcommit: 4d5628b52b84f7564ea893f621bdf1a45113c137
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96443993"
---
# <a name="functions-library"></a>函式程式庫

下列文章包含 [ (使用者定義函數) 的 UDF ](../query/functions/user-defined-functions.md)分類清單。

文章中提供了使用者定義函數程式碼。  它可以在內嵌于查詢中的 let 語句內使用，也可以使用來保存在資料庫中 [`.create function`](../management/create-function.md) 。

## <a name="machine-learning-functions"></a>機器學習函式

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[kmeans_fl()](kmeans-fl.md)|使用 k 表示演算法將叢集化。 |
|[predict_fl()](predict-fl.md)|使用現有的定型機器學習模型進行預測。 |
|[predict_onnx_fl()](predict-onnx-fl.md)| 使用 ONNX 格式的現有定型機器學習模型來預測。 |

## <a name="series-processing-functions"></a>數列處理函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_fl()](quantize-fl.md)|量化計量資料行。 |
|[series_dot_product_fl()](series-dot-product-fl.md)|計算兩個數值向量的點乘積。 |
|[series_downsample_fl()](series-downsample-fl.md)|以整數因數來縮減時間序列。 |
|[series_exp_smoothing_fl()](series-exp-smoothing-fl.md)|在數列上套用基本指數平滑篩選。 |
|[series_fit_lowess_fl()](series-fit-lowess-fl.md)|使用 LOWESS 方法，將局部多項式納入數列。 |
|[series_fit_poly_fl()](series-fit-poly-fl.md)|使用迴歸分析將多項式納入數列。 |
|[series_moving_avg_fl()](series-moving-avg-fl.md)|在數列上套用移動平均篩選。 |
|[series_rolling_fl()](series-rolling-fl.md)|在數列上套用滾動彙總函式。 |
