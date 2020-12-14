---
title: 函數程式庫-Azure 資料總管
description: 本文說明擴充 Azure 資料總管功能的使用者定義函數。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: b7e066133817184664e37aec52a562525afa9504
ms.sourcegitcommit: fcaf3056db2481f0e3f4c2324c4ac956a4afef38
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97388948"
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

## <a name="promql-functions"></a>PromQL 函式

下一節包含一般 [PromQL](https://prometheus.io/docs/prometheus/latest/querying/basics/) 函數。 這些函式可用於分析內嵌至 Azure 的計量（由 [Prometheus](https://prometheus.io/) 監視系統所資料總管）。 所有函式都假設 Azure 資料總管中的計量是使用 [Prometheus 資料模型](https://prometheus.io/docs/concepts/data_model/)進行結構化。


|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[series_metric_fl ( # B1 ](series-metric-fl.md)|選取並取出儲存在 Prometheus 資料模型中的時間序列。 |
|[series_rate_fl ( # B1 ](series-rate-fl.md)|計算每秒計數器度量增加的平均速率。 |

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
