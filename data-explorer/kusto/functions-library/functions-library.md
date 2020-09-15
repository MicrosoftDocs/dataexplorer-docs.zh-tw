---
title: 函數程式庫-Azure 資料總管
description: 本文說明擴充 Azure 資料總管功能的使用者定義函數。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 5b3457d52be37d4c0090db2f34c89994bc829a53
ms.sourcegitcommit: 50c799c60a3937b4c9e81a86a794bdb189df02a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90067533"
---
# <a name="functions-library"></a>函式程式庫

下列文章包含使用者定義函數的分類清單。

## <a name="machine-learning-functions"></a>機器學習函式

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[predict_fl ( # B1 ](predict-fl.md)|使用現有的定型機器學習模型進行預測。 |
|[predict_onnx_fl ( # B1 ](predict-onnx-fl.md)| 使用 ONNX 格式的現有定型機器學習模型來預測。 |

## <a name="series-processing-functions"></a>數列處理函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_fl ( # B1 ](quantize-fl.md)|量化計量資料行。 |
|[series_fit_poly_fl ( # B1 ](series-fit-poly-fl.md)|使用迴歸分析將多項式符合數列。 |
|[series_moving_avg_fl ( # B1 ](series-moving-avg-fl.md)|在數列上套用移動平均篩選。 |
|[series_rolling_fl ( # B1 ](series-rolling-fl.md)|在數列上套用滾動彙總函式。 |
