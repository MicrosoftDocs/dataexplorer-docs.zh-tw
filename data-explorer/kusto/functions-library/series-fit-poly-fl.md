---
title: 'series_fit_poly_fl ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中 ( # A1 使用者定義函數的 series_fit_poly_fl。'
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 454aa19c5b38eabce49fd319891772746335ceab
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321823"
---
# <a name="series_fit_poly_fl"></a>series_fit_poly_fl()

函數會 `series_fit_poly_fl()` 對數列套用多項式回歸。 此函式會採用包含多個數列 (動態數值陣列的資料表) 並使用 [多項式回歸](https://en.wikipedia.org/wiki/Polynomial_regression)為每個數列產生最適合的高序位多項式。 此函數會傳回數列係數和內插多項式的範圍。

> [!NOTE]
> 使用原生函式 [series_fit_poly ( # B1 ](../query/series-fit-poly-function.md)。 下列函式僅供參考。


> [!NOTE]
> * `series_fit_poly_fl()` 是 [UDF (使用者定義函數) ](../query/functions/user-defined-functions.md)。
> * 此函式包含內嵌 Python，且需要在叢集上 [啟用 Python ( # A1 外掛程式](../query/pythonplugin.md#enable-the-plugin) 。 如需詳細資訊，請參閱 [使用](#usage)方式。
> * 若為平均間距的數列（由 [建立數列運算子](../query/make-seriesoperator.md)所建立）的線性回歸，請使用原生函式 [Series_fit_line ( # B1 ](../query/series-fit-linefunction.md)。

## <a name="syntax"></a>語法

`T | invoke series_fit_poly_fl(`*y_series* `,`*y_fit_series* `,`*fit_coeff* `,`*角度* `, [`*x_series* `,`*x_istime*]`)`
  
## <a name="arguments"></a>引數

* *y_series*：包含 [相依變數](https://en.wikipedia.org/wiki/Dependent_and_independent_variables)之輸入資料表資料行的名稱。 也就是要容納的數列。
* *y_fit_series*：儲存最適合數列之資料行的名稱。
* *fit_coeff*：要儲存最適合多項式係數的資料行名稱。
* *度數*：符合之多項式的必要順序。 例如，1表示線性回歸，2用於二次回歸，依此類推。
* *x_series*：包含 [獨立變數](https://en.wikipedia.org/wiki/Dependent_and_independent_variables)的資料行名稱，也就是 x 或時間軸。 這個參數是選擇性的，只有不等 [間距的數列](https://en.wikipedia.org/wiki/Unevenly_spaced_time_series)才需要此參數。 預設值為空字串，因為 x 對於平均間距數列的回歸而言是多餘的。
* *x_istime*：此布林值參數是選擇性的。 只有在指定 *x_series* 且其為 datetime 的向量時，才需要這個參數。

## <a name="usage"></a>使用方式

`series_fit_poly_fl()`這是要使用[invoke 運算子](../query/invokeoperator.md)套用的使用者定義函數[表格式函數](../query/functions/user-defined-functions.md#tabular-function)。 您可以在查詢中內嵌其程式碼，或將其安裝在您的資料庫中。 有兩種使用方式選項：臨機操作和持續使用。 如需範例，請參閱下列索引標籤。

# <a name="ad-hoc"></a>[臨機操作](#tab/adhoc)

針對臨機操作用法，請使用 [let 語句](../query/letstatement.md)內嵌其程式碼。 不需要任何許可權。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_fit_poly_fl=(tbl:(*), y_series:string, y_fit_series:string, fit_coeff:string, degree:int, x_series:string='', x_istime:bool=False)
{
    let kwargs = pack('y_series', y_series, 'y_fit_series', y_fit_series, 'fit_coeff', fit_coeff, 'degree', degree, 'x_series', x_series, 'x_istime', x_istime);
    let code=
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_fit_series = kargs["y_fit_series"]\n'
        'fit_coeff = kargs["fit_coeff"]\n'
        'degree = kargs["degree"]\n'
        'x_series = kargs["x_series"]\n'
        'x_istime = kargs["x_istime"]\n'
        '\n'
        'def fit(ts_row, x_col, y_col, deg):\n'
        '    y = ts_row[y_col]\n'
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
        '    coeff = np.polyfit(x, y, deg)\n'
        '    p = np.poly1d(coeff)\n'
        '    z = p(x)\n'
        '    return z, coeff\n'
        '\n'
        'result = df\n'
        'if len(df):\n'
        '   result[[y_fit_series, fit_coeff]] = df.apply(fit, axis=1, args=(x_series, y_series, degree,), result_type="expand")\n'
    ;
    tbl
     | evaluate python(typeof(*), code, kwargs)
};
//
// Fit fifth order polynomial to a regular (evenly spaced) time series, created with make-series
//
let max_t = datetime(2016-09-03);
demo_make_series1
| make-series num=count() on TimeStamp from max_t-1d to max_t step 5m by OsVer
| extend fnum = dynamic(null), coeff=dynamic(null), fnum1 = dynamic(null), coeff1=dynamic(null)
| invoke series_fit_poly_fl('num', 'fnum', 'coeff', 5)
| render timechart with(ycolumns=num, fnum)
```

# <a name="persistent"></a>[持續](#tab/persistent)

若要持續使用，請使用 [`.create function`](../management/create-function.md) 。  建立函數需要 [資料庫使用者](../management/access-control/role-based-authorization.md)權力。

### <a name="one-time-installation"></a>一次安裝

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Fit a polynomial of a specified degree to a series")
series_fit_poly_fl(tbl:(*), y_series:string, y_fit_series:string, fit_coeff:string, degree:int, x_series:string='', x_istime:bool=false)
{
    let kwargs = pack('y_series', y_series, 'y_fit_series', y_fit_series, 'fit_coeff', fit_coeff, 'degree', degree, 'x_series', x_series, 'x_istime', x_istime);
    let code=
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_fit_series = kargs["y_fit_series"]\n'
        'fit_coeff = kargs["fit_coeff"]\n'
        'degree = kargs["degree"]\n'
        'x_series = kargs["x_series"]\n'
        'x_istime = kargs["x_istime"]\n'
        '\n'
        'def fit(ts_row, x_col, y_col, deg):\n'
        '    y = ts_row[y_col]\n'
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
        '    coeff = np.polyfit(x, y, deg)\n'
        '    p = np.poly1d(coeff)\n'
        '    z = p(x)\n'
        '    return z, coeff\n'
        '\n'
        'result = df\n'
        'if len(df):\n'
        '   result[[y_fit_series, fit_coeff]] = df.apply(fit, axis=1, args=(x_series, y_series, degree,), result_type="expand")\n'
    ;
    tbl
     | evaluate python(typeof(*), code, kwargs)
}
```

### <a name="usage"></a>使用方式

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
// Fit fifth order polynomial to a regular (evenly spaced) time series, created with make-series
//
let max_t = datetime(2016-09-03);
demo_make_series1
| make-series num=count() on TimeStamp from max_t-1d to max_t step 5m by OsVer
| extend fnum = dynamic(null), coeff=dynamic(null), fnum1 = dynamic(null), coeff1=dynamic(null)
| invoke series_fit_poly_fl('num', 'fnum', 'coeff', 5)
| render timechart with(ycolumns=num, fnum)
```

---

:::image type="content" source="images/series-fit-poly-fl/usage-example.png" alt-text="顯示五個順序多項式符合一般時間序列的圖表" border="false":::

## <a name="additional-examples"></a>其他範例

下列範例假設已安裝函數：

1. 測試不規則的 (不等寬的) 時間序列
    
    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    let max_t = datetime(2016-09-03);
    demo_make_series1
    | where TimeStamp between ((max_t-2d)..max_t)
    | summarize num=count() by bin(TimeStamp, 5m), OsVer
    | order by TimeStamp asc
    | where hourofday(TimeStamp) % 6 != 0   //  delete every 6th hour to create unevenly spaced time series
    | summarize TimeStamp=make_list(TimeStamp), num=make_list(num) by OsVer
    | extend fnum = dynamic(null), coeff=dynamic(null)
    | invoke series_fit_poly_fl('num', 'fnum', 'coeff', 8, 'TimeStamp', True)
    | render timechart with(ycolumns=num, fnum)
    ```
    
    :::image type="content" source="images/series-fit-poly-fl/irregular-time-series.png" alt-text="顯示8個順序多項式符合不規則時間序列的圖表" border="false":::

1. X & y 軸上的第五個順序多項式與雜訊

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    range x from 1 to 200 step 1
    | project x = rand()*5 - 2.3
    | extend y = pow(x, 5)-8*pow(x, 3)+10*x+6
    | extend y = y + (rand() - 0.5)*0.5*y
    | summarize x=make_list(x), y=make_list(y)
    | extend y_fit = dynamic(null), coeff=dynamic(null)
    | invoke series_fit_poly_fl('y', 'y_fit', 'coeff', 5, 'x')
    |fork (project-away coeff) (project coeff | mv-expand coeff)
    | render linechart
    ```
        
    :::image type="content" source="images/series-fit-poly-fl/fifth-order-noise.png" alt-text="在 x & y 軸上，符合第五個順序多項式與雜訊的圖表":::
       
    :::image type="content" source="images/series-fit-poly-fl/fifth-order-noise-table.png" alt-text="符合五個順序多項式與雜訊的係數" border="false":::
