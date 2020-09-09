---
title: 函數程式庫-Azure 資料總管
description: 本文說明擴充 Azure 資料總管功能的使用者定義函數。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 66f8da1655fada7429fcd3087810904bd184546c
ms.sourcegitcommit: 993bc7b69096ab5516d3c650b9df97a1f419457b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614509"
---
# <a name="functions-library"></a>函數程式庫

下列文章包含使用者定義函數的分類清單。

## <a name="machine-learning-functions"></a>機器學習函式

|函數名稱     |說明                                          |
|-------------------------|--------------------------------------------------------|
|[predict_lf ( # B1 ](predict-lf.md)|使用現有的定型機器學習模型進行預測。 |
|[predict_onnx_lf ( # B1 ](predict-onnx-lf.md)| 使用 ONNX 格式的現有定型機器學習模型來預測。 |

## <a name="series-processing-functions"></a>數列處理函數

|函數名稱     |說明                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_lf ( # B1 ](quantize-lf.md)|量化計量資料行。 |
|[series_fit_poly_lf ( # B1 ](series-fit-poly-lf.md)|使用迴歸分析將多項式符合數列。 |
|[series_moving_avg_lf ( # B1 ](series-moving-avg-lf.md)|在數列上套用移動平均篩選。 |
|[series_rolling_lf ( # B1 ](series-rolling-lf.md)|在數列上套用滾動彙總函式。 |
