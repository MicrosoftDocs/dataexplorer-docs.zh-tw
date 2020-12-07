---
title: 'series_fit_lowess_fl ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中 ( # A1 使用者定義函數的 series_fit_lowess_fl。'
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 11/29/2020
no-loc: LOWESS
ms.openlocfilehash: da384b1a907e4b524fc40f1c4133be5cf8e21c9c
ms.sourcegitcommit: 4d5628b52b84f7564ea893f621bdf1a45113c137
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96469742"
---
# <a name="series_fit_lowess_fl"></a>series_fit_lowess_fl()

函數會對 `series_fit_lowess_fl()` 數列套用[ LOWESS 回歸](https://www.wikipedia.org/wiki/Local_regression)。 此函式會採用具有多個數列 (動態數值陣列的資料表) 並產生 *LOWESS 曲線*，也就是原始數列的平滑版本。

> [!NOTE]
> * `series_fit_lowess_fl()` 是 [UDF (使用者定義函數) ](../query/functions/user-defined-functions.md)。
> * 此函式包含內嵌 Python，且需要在叢集上 [啟用 Python ( # A1 外掛程式](../query/pythonplugin.md#enable-the-plugin) 。 如需詳細資訊，請參閱 [使用](#usage)方式。

## <a name="syntax"></a>語法

`T | invoke series_fit_lowess_fl(`*y_series* `,`*y_fit_series* `, [`*fit_size* `, `*x_series* `,`*x_istime*]`)`

## <a name="arguments"></a>引數

* *y_series*：包含 [相依變數](https://www.wikipedia.org/wiki/Dependent_and_independent_variables)之輸入資料表資料行的名稱。 此資料行是要容納的數列。
* *y_fit_series*：要儲存適合數列的資料行名稱。
* *fit_size*：針對每個點，會在其各自的 *fit_size* 最接近的點上套用區域回歸。 這個參數是選擇性的，預設值為 *5*。
* *x_series*：包含 [獨立變數](https://www.wikipedia.org/wiki/Dependent_and_independent_variables)的資料行名稱，也就是 x 或時間軸。 這個參數是選擇性的，只有不等 [間距的數列](https://www.wikipedia.org/wiki/Unevenly_spaced_time_series)才需要此參數。 預設值為空字串，因為 x 對於平均間距數列的回歸而言是多餘的。
* *x_istime*：只有在指定 *x_series* 且其為 datetime 的向量時，才需要此布林值參數。 這個參數是選擇性的，預設值為 *False*。

## <a name="usage"></a>使用方式

`series_fit_lowess_fl()`這是要使用[invoke 運算子](../query/invokeoperator.md)套用的使用者定義函數[表格式函數](../query/functions/user-defined-functions.md#tabular-function)。 您可以在查詢中內嵌其程式碼，或將其安裝在您的資料庫中。 有兩種使用方式選項：臨機操作和持續使用。 如需範例，請參閱下列索引標籤。

# <a name="ad-hoc"></a>[臨機操作](#tab/adhoc)

針對臨機操作用法，請使用 [let 語句](../query/letstatement.md)內嵌其程式碼。 不需要任何許可權。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_fit_lowess_fl=(tbl:(*), y_series:string, y_fit_series:string, fit_size:int=5, x_series:string='', x_istime:bool=False)
{
    let kwargs = pack('y_series', y_series, 'y_fit_series', y_fit_series, 'fit_size', fit_size, 'x_series', x_series, 'x_istime', x_istime);
    let code=
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_fit_series = kargs["y_fit_series"]\n'
        'fit_size = kargs["fit_size"]\n'
        'x_series = kargs["x_series"]\n'
        'x_istime = kargs["x_istime"]\n'
        '\n'
        'import statsmodels.api as sm\n'
        'def lowess_fit(ts_row, x_col, y_col, fsize):\n'
        '    y = ts_row[y_col]\n'
        '    fraction = fsize/len(y)\n'
        '    if x_col == "": # If there is no x column creates sequential range [1, len(y)]\n'
        '       x = np.arange(len(y)) + 1\n'
        '    else: # if x column exists check whether its a time column. If so, normalize it to the [1, len(y)] range, else take it as is.\n'
        '       if x_istime: \n'
        '           x = pd.to_numeric(pd.to_datetime(ts_row[x_col]))\n'
        '           x = x - x.min()\n'
        '           x = x / x.max()\n'
        '           x = x * (len(x) - 1) + 1\n'
        '       else:\n'
        '           x = ts_row[x_col]\n'
        '    lowess = sm.nonparametric.lowess\n'
        '    z = lowess(y, x, return_sorted=False, frac=fraction)\n'
        '    return list(z)\n'
        '\n'
        'result = df\n'
        'result[y_fit_series] = df.apply(lowess_fit, axis=1, args=(x_series, y_series, fit_size))\n'
    ;
    tbl
     | evaluate python(typeof(*), code, kwargs)
};
//
// Apply 9 points LOWESS regression on regular time series
//
let max_t = datetime(2016-09-03);
demo_make_series1
| make-series num=count() on TimeStamp from max_t-1d to max_t step 5m by OsVer
| extend fnum = dynamic(null)
| invoke series_fit_lowess_fl('num', 'fnum', 9)
| render timechart
```

# <a name="persistent"></a>[持續](#tab/persistent)

若要持續使用，請使用 [`.create function`](../management/create-function.md) 。  建立函數需要 [資料庫使用者](../management/access-control/role-based-authorization.md)權力。

### <a name="one-time-installation"></a>一次安裝

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Fits a local polynomial using LOWESS method to a series")
series_fit_lowess_fl(tbl:(*), y_series:string, y_fit_series:string, fit_size:int=5, x_series:string='', x_istime:bool=False)
{
    let kwargs = pack('y_series', y_series, 'y_fit_series', y_fit_series, 'fit_size', fit_size, 'x_series', x_series, 'x_istime', x_istime);
    let code=
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_fit_series = kargs["y_fit_series"]\n'
        'fit_size = kargs["fit_size"]\n'
        'x_series = kargs["x_series"]\n'
        'x_istime = kargs["x_istime"]\n'
        '\n'
        'import statsmodels.api as sm\n'
        'def lowess_fit(ts_row, x_col, y_col, fsize):\n'
        '    y = ts_row[y_col]\n'
        '    fraction = fsize/len(y)\n'
        '    if x_col == "": # If there is no x column creates sequential range [1, len(y)]\n'
        '       x = np.arange(len(y)) + 1\n'
        '    else: # if x column exists check whether its a time column. If so, normalize it to the [1, len(y)] range, else take it as is.\n'
        '       if x_istime: \n'
        '           x = pd.to_numeric(pd.to_datetime(ts_row[x_col]))\n'
        '           x = x - x.min()\n'
        '           x = x / x.max()\n'
        '           x = x * (len(x) - 1) + 1\n'
        '       else:\n'
        '           x = ts_row[x_col]\n'
        '    lowess = sm.nonparametric.lowess\n'
        '    z = lowess(y, x, return_sorted=False, frac=fraction)\n'
        '    return list(z)\n'
        '\n'
        'result = df\n'
        'result[y_fit_series] = df.apply(lowess_fit, axis=1, args=(x_series, y_series, fit_size))\n'
    ;
    tbl
     | evaluate python(typeof(*), code, kwargs)
}
```

### <a name="usage"></a>使用方式

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
// Apply 9 points LOWESS regression on regular time series
//
let max_t = datetime(2016-09-03);
demo_make_series1
| make-series num=count() on TimeStamp from max_t-1d to max_t step 5m by OsVer
| extend fnum = dynamic(null)
| invoke series_fit_lowess_fl('num', 'fnum', 9)
| render timechart
```

---

:::image type="content" source="images/series-fit-lowess-fl/lowess-regular-time-series.png" alt-text="Graph showing nine points :::非 loc (LOWESS) ：：：符合一般時間序列 "border =" false "：：：

## <a name="examples"></a>範例

下列範例假設已安裝函數：

### <a name="test-irregular-time-series"></a>測試不規則的時間序列

下列範例會測試不規則的 (不等寬的) 時間序列
    
    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    let max_t = datetime(2016-09-03);
    demo_make_series1
    | where TimeStamp between ((max_t-1d)..max_t)
    | summarize num=count() by bin(TimeStamp, 5m), OsVer
    | order by TimeStamp asc
    | where hourofday(TimeStamp) % 6 != 0   //  delete every 6th hour to create irregular time series
    | summarize TimeStamp=make_list(TimeStamp), num=make_list(num) by OsVer
    | extend fnum = dynamic(null)
    | invoke series_fit_lowess_fl('num', 'fnum', 9, 'TimeStamp', True)
    | render timechart 
    ```
    
    :::image type="content" source="images/series-fit-lowess-fl/lowess-irregular-time-series.png" alt-text="Graph showing nine points LOWESS fit to an irregular time series" border="false":::

比較 LOWESS 與多項式相符

下列範例包含在 x 和 y 軸上有雜訊的第五個順序多項式。 請參閱 LOWESS 與多項式相符的比較。 

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    range x from 1 to 200 step 1
    | project x = rand()*5 - 2.3
    | extend y = pow(x, 5)-8*pow(x, 3)+10*x+6
    | extend y = y + (rand() - 0.5)*0.5*y
    | summarize x=make_list(x), y=make_list(y)
    | extend y_lowess = dynamic(null)
    | invoke series_fit_lowess_fl('y', 'y_lowess', 15, 'x')
    | extend series_fit_poly(y, x, 5)
    | project x, y, y_lowess, y_polynomial=series_fit_poly_y_poly_fit
    | render linechart
    ```
        
    :::image type="content" source="images/series-fit-lowess-fl/lowess-vs-poly-fifth-order-noise.png" alt-text="Graphs of LOWESS vs polynomial fit for a fifth order polynomial with noise on x & y axes":::
