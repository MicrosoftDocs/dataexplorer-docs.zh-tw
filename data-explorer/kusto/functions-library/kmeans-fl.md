---
title: 'kmeans_fl ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中 ( # A1 使用者定義函數的 kmeans_fl。'
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 10/18/2020
ms.openlocfilehash: 7d2482c36c0c55c34adbc664a6c24f64bdadf7a1
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321942"
---
# <a name="kmeans_fl"></a>kmeans_fl()

此函數會 `kmeans_fl()` 使用 [k 表示演算法](https://en.wikipedia.org/wiki/K-means_clustering)clusterizes 資料集。

> [!NOTE]
> * `kmeans_fl()` 是 [UDF (使用者定義函數) ](../query/functions/user-defined-functions.md)。
> * 此函式包含內嵌 Python，且需要在叢集上 [啟用 Python ( # A1 外掛程式](../query/pythonplugin.md#enable-the-plugin) 。 如需詳細資訊，請參閱 [使用](#usage)方式。

## <a name="syntax"></a>語法

`T | invoke kmeans_fl(`*k* `,` *features_cols* `,` *cluster_col*`)`

## <a name="arguments"></a>引數

* *k*：需要的群集數目。
* *features_cols*：動態陣列，包含用於群集的功能資料行名稱。
* *cluster_col*：儲存每一筆記錄之輸出叢集識別碼的資料行名稱。

## <a name="usage"></a>使用方式

`kmeans_fl()`這是要使用[invoke 運算子](../query/invokeoperator.md)套用的使用者定義[表格式函數](../query/functions/user-defined-functions.md#tabular-function)。 您可以在查詢中內嵌其程式碼，或將其安裝在您的資料庫中。 有兩種使用方式選項：臨機操作和持續使用。 如需範例，請參閱下列索引標籤。

# <a name="ad-hoc"></a>[臨機操作](#tab/adhoc)

針對臨機操作使用方式，請使用 [let 語句](../query/letstatement.md)內嵌程式碼。 不需要任何許可權。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let kmeans_fl=(tbl:(*), k:int, features:dynamic, cluster_col:string)
{
    let kwargs = pack('k', k, 'features', features, 'cluster_col', cluster_col);
    let code =
        '\n'
        'from sklearn.cluster import KMeans\n'
        '\n'
        'k = kargs["k"]\n'
        'features = kargs["features"]\n'
        'cluster_col = kargs["cluster_col"]\n'
        '\n'
        'km = KMeans(n_clusters=k)\n'
        'df1 = df[features]\n'
        'km.fit(df1)\n'
        'result = df\n'
        'result[cluster_col] = km.labels_\n'
        ;
    tbl
    | evaluate python(typeof(*), code, kwargs)
};
//
// Clusterize room occupancy from sensors measurements.
//
// Occupancy Detection is an open dataset from UCI Repository at https://archive.ics.uci.edu/ml/datasets/Occupancy+Detection+
// It contains experimental data for binary classification of room occupancy from Temperature, Humidity, Light, and CO2.
//
OccupancyDetection
| extend cluster_id=double(null)
| invoke kmeans_fl(5, pack_array("Temperature", "Humidity", "Light", "CO2", "HumidityRatio"), "cluster_id")
| sample 10
```

# <a name="persistent"></a>[持續](#tab/persistent)

若要持續使用，請使用 [`.create function`](../management/create-function.md) 。 建立函數需要 [資料庫使用者](../management/access-control/role-based-authorization.md)權力。

### <a name="one-time-installation"></a>一次安裝

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create function with (folder = "Packages\\ML", docstring = "K-Means clustering")
kmeans_fl(tbl:(*), k:int, features:dynamic, cluster_col:string)
{
    let kwargs = pack('k', k, 'features', features, 'cluster_col', cluster_col);
    let code =
        '\n'
        'from sklearn.cluster import KMeans\n'
        '\n'
        'k = kargs["k"]\n'
        'features = kargs["features"]\n'
        'cluster_col = kargs["cluster_col"]\n'
        '\n'
        'km = KMeans(n_clusters=k)\n'
        'df1 = df[features]\n'
        'km.fit(df1)\n'
        'result = df\n'
        'result[cluster_col] = km.labels_\n'
        ;
    tbl
    | evaluate python(typeof(*), code, kwargs)
}
```

### <a name="usage"></a>使用方式

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
// Clusterize room occupancy from sensors measurements.
//
// Occupancy Detection is an open dataset from UCI Repository at https://archive.ics.uci.edu/ml/datasets/Occupancy+Detection+
// It contains experimental data for binary classification of room occupancy from Temperature, Humidity, Light, and CO2.
//
OccupancyDetection
| extend cluster_id=double(null)
| invoke kmeans_fl(5, pack_array("Temperature", "Humidity", "Light", "CO2", "HumidityRatio"), "cluster_id")
| sample 10
```

---

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
Timestamp                   Temperature Humidity    Light  CO2         HumidityRatio Occupancy Test  cluster_id
2015-02-02 14:38:00.0000000 23.64       27.1        473    908.8       0.00489763    TRUE      TRUE  1
2015-02-03 01:47:00.0000000 20.575      22.125      0      446         0.00330878    FALSE     TRUE  0
2015-02-10 08:47:00.0000000 20.42666667 33.56       405    494.3333333 0.004986493   TRUE      FALSE 4
2015-02-10 09:15:00.0000000 20.85666667 35.09666667 433    665.3333333 0.005358055   TRUE      FALSE 4
2015-02-11 16:13:00.0000000 21.89       30.0225     429    771.75      0.004879358   TRUE      TRUE  4
2015-02-13 14:06:00.0000000 23.4175     26.5225     608    599.75      0.004728116   TRUE      TRUE  4
2015-02-13 23:09:00.0000000 20.13333333 32.2        0      502.6666667 0.004696278   FALSE     TRUE  0
2015-02-15 18:30:00.0000000 20.5        32.79       0      666.5       0.004893459   FALSE     TRUE  3
2015-02-17 13:43:00.0000000 21.7        33.9        454    1167        0.005450924   TRUE      TRUE  1
2015-02-17 18:17:00.0000000 22.025      34.2225     0      1538.25     0.005614538   FALSE     TRUE  2
```

使用已安裝的函式，將每個群集的距心和大小解壓縮：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
OccupancyDetection
| extend cluster_id=double(null)
| invoke kmeans_fl(5, pack_array("Temperature", "Humidity", "Light", "CO2", "HumidityRatio"), "cluster_id")
| summarize Temperature=avg(Temperature), Humidity=avg(Humidity), Light=avg(Light), CO2=avg(CO2), HumidityRatio=avg(HumidityRatio), num=count() by cluster_id
| order by num

```

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
cluster_id  Temperature        Humidity            Light            CO2                HumidityRatio        num
0           20.3507186863278   27.1521395395781    10.1995789883291 486.804272186974   0.00400132147662714  11124
4           20.9247315268427   28.7971160082823    20.7311894656536 748.965771574469   0.00440412568299058  3063
1           22.0284137970445   27.8953334469889    481.872136037748 1020.70779349773   0.00456692559904535  2514
3           22.0344177115763   25.1151053429273    462.358969056434 656.310608696507   0.00411782436443015  2176
2           21.4091216082295   31.8363099552939    174.614816229606 1482.05062388414   0.00504573022875817  1683
```
