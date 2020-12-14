---
title: 'series_rate_fl ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中 ( # A1 使用者定義函數的 series_rate_fl。'
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 12/13/2020
ms.openlocfilehash: 6678f17624225895734781f32782ed5b9bd79955
ms.sourcegitcommit: fcaf3056db2481f0e3f4c2324c4ac956a4afef38
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97389125"
---
# <a name="series_rate_fl"></a>series_rate_fl ( # A1


函數會 `series_rate_fl()` 計算每秒增加的度量平均速率。 其邏輯遵循 PromQL [速率 ( # B1 ](https://prometheus.io/docs/prometheus/latest/querying/functions/#rate) 函數。 它應該用於 [Prometheus](https://prometheus.io/) 監視系統內嵌至 Azure 資料總管的計數器計量時間序列，並 [Series_metric_fl ( # B1 ](series-metric-fl.md)取出。

> [!NOTE]
>`series_rate_fl()` 是 [UDF (使用者定義函數) ](../query/functions/user-defined-functions.md)。 如需詳細資訊，請參閱 [使用](#usage)方式。

## <a name="syntax"></a>語法

`T | invoke series_rate_fl(`*n_bins*`)`

`T` 這是從 [series_metric_fl ( # B1 ](series-metric-fl.md)傳回的資料表。 其架構包含 `(timestamp:dynamic, name:string, labels:string, value:dynamic)` 。

## <a name="arguments"></a>引數

* *n_bins*：用來指定已解壓縮的計量值與比率計算之間間距的 bin 數目。 函式會計算目前樣本與之前 *n_bins* 之間的差異，並將其除以各自的時間戳記差異（以秒為單位）。 這個參數是選擇性的，預設值是一個 bin。 預設設定會計算 [irate ( # B1 ](https://prometheus.io/docs/prometheus/latest/querying/functions/#irate)，也就是 PromQL 的暫態速率函數。

## <a name="usage"></a>使用方式

`series_rate_fl()`這是要使用[invoke 運算子](../query/invokeoperator.md)套用的使用者定義[表格式函數](../query/functions/user-defined-functions.md#tabular-function)。 您可以在查詢中內嵌其程式碼，或將其安裝在您的資料庫中。 有兩種使用方式選項：臨機操作和持續使用。 如需範例，請參閱下列索引標籤。

> [!NOTE]
> [series_metric_fl ( # B1 ](series-metric-fl.md) 可作為函式的一部分，而且必須安裝或內嵌。

# <a name="ad-hoc"></a>[臨機操作](#tab/adhoc)

針對臨機操作使用方式，請使用 [let 語句](../query/letstatement.md)內嵌其程式碼。 不需要任何許可權。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_rate_fl=(tbl:(timestamp:dynamic, name:string, labels:string, value:dynamic), n_bins:int=1)
{
    tbl
    | extend timestampS = array_shift_right(timestamp, n_bins), valueS = array_shift_right(value, n_bins)
    | extend dt = series_subtract(timestamp, timestampS)
    | extend dt = series_divide(dt, 1e7)                              //  converts from ticks to seconds
    | extend dv = series_subtract(value, valueS)
    | extend dv = array_iff(series_greater_equals(dv, 0), dv, value)  //  handles negative difference like PromQL
    | extend rate = series_divide(dv, dt)
    | project timestamp, name, rate, labels
}
;
//
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', offset=now()-datetime(2020-12-08 00:00))
| invoke series_rate_fl(2)
| render timechart with(series=labels)
```

# <a name="persistent"></a>[持續](#tab/persistent)

若要持續使用，請使用 [`.create function`](../management/create-function.md) 。 建立函數需要 [資料庫使用者](../management/access-control/role-based-authorization.md)權力。

### <a name="one-time-installation"></a>一次安裝

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Simulate PromQL rate()")
series_rate_fl(tbl:(timestamp:dynamic, name:string, labels:string, value:dynamic), n_bins:int=1)
{
    tbl
    | extend timestampS = array_shift_right(timestamp, n_bins), valueS = array_shift_right(value, n_bins)
    | extend dt = series_subtract(timestamp, timestampS)
    | extend dt = series_divide(dt, 1e7)                              //  converts from ticks to seconds
    | extend dv = series_subtract(value, valueS)
    | extend dv = array_iff(series_greater_equals(dv, 0), dv, value)  //  handles negative difference like PromQL
    | extend rate = series_divide(dv, dt)
    | project timestamp, name, rate, labels
}
```

### <a name="usage"></a>使用方式

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', offset=now()-datetime(2020-12-08 00:00))
| invoke series_rate_fl(2)
| render timechart with(series=labels)
```

---

:::image type="content" source="images/series-rate-fl/all-disks-write-rate-2-bins.png" alt-text="顯示所有磁片的磁片寫入度量每秒速率的圖表" border="false":::

## <a name="example"></a>範例

下列範例會選取兩部主機的主要磁片，並假設已安裝此函式。 此範例使用替代的直接呼叫語法，將輸入資料表指定為第一個參數：
    
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
series_rate_fl(series_metric_fl(demo_prometheus, 'TimeStamp', 'Name', 'Labels', 'Val', 'writes', 'disk:sda1', lookback=2h, offset=now()-datetime(2020-12-08 00:00)), n_bins=10)
| render timechart with(series=labels)
```
    
:::image type="content" source="images/series-rate-fl/main-disks-write-rate-10-bins.png" alt-text="此圖表顯示過去兩小時內主要磁片寫入計量的每秒速率，且有10個區間間距" border="false":::
