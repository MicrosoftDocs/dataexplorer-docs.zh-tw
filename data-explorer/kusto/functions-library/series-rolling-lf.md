---
title: 'series_rolling_lf ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中 ( # A1 使用者定義函數的 series_rolling_lf。'
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 50637f0b943c0ae8908f5943c179ab00ff6ef048
ms.sourcegitcommit: 993bc7b69096ab5516d3c650b9df97a1f419457b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614720"
---
# <a name="series_rolling_lf"></a>series_rolling_lf ( # A1


函數會 `series_rolling_lf()` 在數列上套用輪流匯總。 它會採用一個包含多個數列 (動態數值陣列的資料表) 並適用于每個數列的滾動彙總函式。

> [!NOTE]
> `series_rolling_lf()` 是 [UDF (使用者定義函數) ](../query/functions/user-defined-functions.md)。  此函式會 ontains 內嵌 Python，並需要在叢集上 [啟用 Python ( # A1 外掛程式](../query/pythonplugin.md#enable-the-plugin) 。 如需詳細資訊，請參閱 [使用](#usage)方式。

## <a name="syntax"></a>語法

`T | invoke series_rolling_lf(`*y_series* `,`*y_rolling_series* `,`*n* `,` *aggr* `,` *aggr_params* `,` *中心*`)`

## <a name="arguments"></a>引數

* *y_series*：輸入資料表的資料行 (名稱，) 包含要容納的數列。
* *y_rolling_series*：要儲存滾動匯總數列的資料行名稱。
* *n*：滾動視窗的寬度。
* *aggr*：要使用的彙總函式名稱。 請參閱 [彙總函式](#aggregation-functions)。
* *aggr_params*：彙總函式的選擇性參數。
* *置*中：選擇性的布林值，指出滾動視窗是否為下列其中一個選項：
    * 在目前的點前後套用對稱套用，或 
    * 從目前的點向後套用。 <br>
    依預設， *center* 為 false，用於計算串流資料。

## <a name="aggregation-functions"></a>彙總函式

此函數支援從 [numpy](https://numpy.org/) 或 [scipy](https://docs.scipy.org/doc/scipy/reference/stats.html#module-scipy.stats) 計算純量的任何彙總函式。 下列清單不完整：

* [`sum`](https://numpy.org/doc/stable/reference/generated/numpy.sum.html#numpy.sum) 
* [`mean`](https://numpy.org/doc/stable/reference/generated/numpy.mean.html?highlight=mean#numpy.mean)
* [`min`](https://numpy.org/doc/stable/reference/generated/numpy.amin.html#numpy.amin)
* [`max`](https://numpy.org/doc/stable/reference/generated/numpy.amax.html)
* [`ptp (max-min)`](https://numpy.org/doc/stable/reference/generated/numpy.ptp.html)
* [`percentile`](https://numpy.org/doc/stable/reference/generated/numpy.percentile.html)
* [`median`](https://numpy.org/doc/stable/reference/generated/numpy.median.html)
* [`std`](https://numpy.org/doc/stable/reference/generated/numpy.std.html)
* [`var`](https://numpy.org/doc/stable/reference/generated/numpy.var.html)
* [`gmean` (幾何平均數) ](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.gmean.html)
* [`hmean` (調和平均) ](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.hmean.html)
* [`mode` (最常見的值) ](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.mode.html)
* [`moment` (n<sup>時間</sup>) ](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.moment.html)
* [`tmean` (修剪的平均值) ](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.tmean.html)
* [`tmin`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.tmin.html) 
* [`tmax`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.tmax.html)
* [`tstd`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.tstd.html)
* [`iqr` (分量範圍間) ](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.iqr.html) 

## <a name="usage"></a>使用方式

`series_rolling_lf()`這是要使用[invoke 運算子](../query/invokeoperator.md)套用的使用者定義[表格式函數](../query/functions/user-defined-functions.md#tabular-function)。 您可以在查詢中內嵌其程式碼，或將其安裝在您的資料庫中。 有兩種使用方式選項：臨機操作和持續使用。 如需範例，請參閱下列索引標籤。

# <a name="ad-hoc"></a>[臨機操作](#tab/adhoc)

針對臨機操作用法，請使用 [let 語句](../query/letstatement.md)內嵌其程式碼。 不需要任何許可權。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_rolling_udf = (tbl:(*), y_series:string, y_rolling_series:string, n:int, aggr:string, aggr_params:dynamic=dynamic([null]), center:bool=true)
{
    let kwargs = pack('y_series', y_series, 'y_rolling_series', y_rolling_series, 'n', n, 'aggr', aggr, 'aggr_params', aggr_params, 'center', center);
    let code =
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_rolling_series = kargs["y_rolling_series"]\n'
        'n = kargs["n"]\n'
        'aggr = kargs["aggr"]\n'
        'aggr_params = kargs["aggr_params"]\n'
        'center = kargs["center"]\n'
        'result = df\n'
        'in_s = df[y_series]\n'
        'func = getattr(np, aggr, None)\n'
        'if not func:\n'
        '    import scipy.stats\n'
        '    func = getattr(scipy.stats, aggr)\n'
        'if func:\n'
        '    result[y_rolling_series] = list(pd.Series(in_s[i]).rolling(n, center=center, min_periods=1).apply(func, args=aggr_params).values for i in range(len(in_s)))\n'
        '\n';
    tbl
    | evaluate python(typeof(*), code, kwargs)
}
;
//
//  Calculate rolling median of 9 elements
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend rolling_med = dynamic(null)
| invoke series_rolling_lf('num', 'rolling_med', 9, 'median')
| render timechart
```

# <a name="persistent"></a>[持續](#tab/persistent)

若要持續使用，請使用 [. create function](../management/create-function.md)。 建立函數需要 [資料庫使用者](../management/access-control/role-based-authorization.md)權力。

### <a name="one-time-installation"></a>一次安裝

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Rolling window functions on a series")
series_rolling_lf(tbl:(*), y_series:string, y_rolling_series:string, n:int, aggr:string, aggr_params:dynamic, center:bool=true)
{
    let kwargs = pack('y_series', y_series, 'y_rolling_series', y_rolling_series, 'n', n, 'aggr', aggr, 'aggr_params', aggr_params, 'center', center);
    let code =
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_rolling_series = kargs["y_rolling_series"]\n'
        'n = kargs["n"]\n'
        'aggr = kargs["aggr"]\n'
        'aggr_params = kargs["aggr_params"]\n'
        'center = kargs["center"]\n'
        'result = df\n'
        'in_s = df[y_series]\n'
        'func = getattr(np, aggr, None)\n'
        'if not func:\n'
        '    import scipy.stats\n'
        '    func = getattr(scipy.stats, aggr)\n'
        'if func:\n'
        '    result[y_rolling_series] = list(pd.Series(in_s[i]).rolling(n, center=center, min_periods=1).apply(func, args=aggr_params).values for i in range(len(in_s)))\n'
        '\n';
    tbl
    | evaluate python(typeof(*), code, kwargs)
}
```

### <a name="usage"></a>使用方式

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  Calculate rolling median of 9 elements
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend rolling_med = dynamic(null)
| invoke series_rolling_lf('num', 'rolling_med', 9, 'median', dynamic([null]))
| render timechart
```

---

:::image type="content" source="images/series-rolling-lf/rolling-median-9.png" alt-text="描繪9個元素之變換中間值的圖表" border="false":::

## <a name="additional-examples"></a>其他範例

下列範例假設已安裝函數：

1. 計算75百分之15的最小值、最大值 & 百分位數
    
    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    //
    //  Calculate rolling min, max & 75th percentile of 15 elements
    //
    demo_make_series1
    | make-series num=count() on TimeStamp step 1h by OsVer
    | extend rolling_min = dynamic(null), rolling_max = dynamic(null), rolling_pct = dynamic(null)
    | invoke series_rolling_lf('num', 'rolling_min', 15, 'min', dynamic([null]))
    | invoke series_rolling_lf('num', 'rolling_max', 15, 'max', dynamic([null]))
    | invoke series_rolling_lf('num', 'rolling_pct', 15, 'percentile', dynamic([75]))
    | render timechart
    ```
    
    :::image type="content" source="images/series-rolling-lf/graph-rolling-15.png" alt-text="描述滾動 min、max & 75 百分之15元素的圖形" border="false":::

1. 計算輪流修剪平均數
        
    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    //
    //  Calculate rolling trimmed mean
    //
    range x from 1 to 100 step 1
    | extend y=iff(x % 13 == 0, 2.0, iff(x % 23 == 0, -2.0, rand()))
    | summarize x=make_list(x), y=make_list(y)
    | extend yr = dynamic(null)
    | invoke series_rolling_lf('y', 'yr', 7, 'tmean', pack_array(pack_array(-2, 2), pack_array(false, false))) //  trimmed mean: ignoring values outside [-2,2] inclusive
    | render linechart
    ```
    
    :::image type="content" source="images/series-rolling-lf/rolling-trimmed-mean.png" alt-text="描繪輪流修剪平均值的圖表" border="false":::
    