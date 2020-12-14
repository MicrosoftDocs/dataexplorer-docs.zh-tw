---
title: 'series_metric_fl ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中 ( # A1 使用者定義函數的 series_metric_fl。'
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 12/13/2020
ms.openlocfilehash: 2af82906be2cdbffdc69646a3050376e0a1af867
ms.sourcegitcommit: fcaf3056db2481f0e3f4c2324c4ac956a4afef38
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97389129"
---
# <a name="series_metric_fl"></a>series_metric_fl ( # A1


此函式會 `series_metric_fl()` 使用 [Prometheus](https://prometheus.io/) 監視系統來選取並取出內嵌至 Azure 資料總管計量的時間序列。 此函式會假設儲存在 Azure 資料總管中的資料結構在 [Prometheus 資料模型](https://prometheus.io/docs/concepts/data_model/)之後。 具體來說，每一筆記錄都包含：
 * timestamp 
 * 度量名稱 
 * 度量值 
 * 標籤 (組的變數集 `key:value`) 
 
 Prometheus 會依據其計量名稱和一組不同的標籤來定義時間序列。 您可以使用 [Prometheus 查詢語言 (PromQL) ](https://prometheus.io/docs/prometheus/latest/querying/basics/) ，藉由指定 [計量名稱] 和 [時間序列選取器] (一組標籤) ，來取得時間序列的組合。

> [!NOTE]
> `series_metric_fl()` 是 [UDF (使用者定義函數) ](../query/functions/user-defined-functions.md)。 如需詳細資訊，請參閱 [使用](#usage)方式。

## <a name="syntax"></a>語法

`T | invoke series_metric_fl(`*timestamp_col* `,`*name_col* `,`*labels_col* `,`*value_col* `,`*metric_name* `,`*labels_selector* `,`*回顧* `,`*位移*`)`

## <a name="arguments"></a>引數

* *timestamp_col*：包含時間戳記的資料行名稱。
* *name_col*：包含度量名稱的資料行名稱。
* *labels_col*：包含標籤字典之資料行的名稱。
* *value_col*：包含度量值的資料行名稱。
* *metric_name*：要取出的度量時間序列。
* *labels_selector*：時間序列選取器字串， [類似于 PromQL](https://prometheus.io/docs/prometheus/latest/querying/basics/#time-series-selectors)。 例如，它是包含配對清單的字串 `key:value` `'key1:val1,key2:val2'` 。 這個參數是選擇性的，預設值為空字串，表示不進行篩選。 請注意，不支援正則運算式。 
* *回顧*：要取出的範圍向量， [類似于 PromQL](https://prometheus.io/docs/prometheus/latest/querying/basics/#range-vector-selectors)。 這個參數是選擇性的，預設值為10分鐘。
* *位移*：從目前的時間位移回抓取， [類似于 PromQL](https://prometheus.io/docs/prometheus/latest/querying/basics/#offset-modifier)。 資料會從 *之前取出 (offset) -回顧* 至 *前 (位移)*。 這個參數是選擇性的，預設值為0，這表示資料會先取出 ( # A1。

## <a name="usage"></a>使用方式

`series_metric_fl()`這是要使用[invoke 運算子](../query/invokeoperator.md)套用的使用者定義[表格式函數](../query/functions/user-defined-functions.md#tabular-function)。 您可以在查詢中內嵌其程式碼，或將其安裝在您的資料庫中。 有兩種使用方式選項：臨機操作和持續使用。 如需範例，請參閱下列索引標籤。

# <a name="ad-hoc"></a>[臨機操作](#tab/adhoc)

針對臨機操作使用方式，請使用 [let 語句](../query/letstatement.md)內嵌其程式碼。 不需要任何許可權。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_metric_fl=(metrics_tbl:(*), timestamp_col:string, name_col:string, labels_col:string, value_col:string, metric_name:string, labels_selector:string='', lookback:timespan=timespan(10m), offset:timespan=timespan(0))
{
    let selector_d=iff(labels_selector == '', dynamic(['']), split(strcat('"', replace('([:,])','"\\1"', labels_selector), '"'), ','));
    let etime = ago(offset);
    let stime = etime - lookback;
    metrics_tbl
    | extend timestamp = column_ifexists(timestamp_col, datetime(null)), name = column_ifexists(name_col, ''), labels = column_ifexists(labels_col, dynamic(null)), value = column_ifexists(value_col, 0)
    | extend labels = dynamic_to_json(labels)       //  convert to string and sort by key
    | where name == metric_name and timestamp between(stime..etime)
    | project timestamp, value, name, labels
    | order by timestamp asc
    | summarize timestamp = make_list(timestamp), value=make_list(value) by name, labels
    //  KQL has native has_any(), but no native has_all(), the lines below implement has_all()
    | mv-apply x = selector_d to typeof(string) on (
      summarize countif(labels has x)
      | where countif_ == array_length(selector_d)
    )
    | project-away countif_
}
;
//
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', 'disk:sda1,host:aks-agentpool-88086459-vmss000001', offset=now()-datetime(2020-12-08 00:00))
| render timechart with(series=labels)
```

# <a name="persistent"></a>[持續](#tab/persistent)

若要持續使用，請使用 [`.create function`](../management/create-function.md) 。 建立函數需要 [資料庫使用者](../management/access-control/role-based-authorization.md)權力。

### <a name="one-time-installation"></a>一次安裝

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create function with (folder = "Packages\\Series", docstring = "Selecting & retrieving metrics like PromQL")
series_metric_fl(metrics_tbl:(*), timestamp_col:string, name_col:string, labels_col:string, value_col:string, metric_name:string, labels_selector:string='', lookback:timespan=timespan(10m), offset:timespan=timespan(0))
{
    let selector_d=iff(labels_selector == '', dynamic(['']), split(strcat('"', replace('([:,])','"\\1"', labels_selector), '"'), ','));
    let etime = ago(offset);
    let stime = etime - lookback;
    metrics_tbl
    | extend timestamp = column_ifexists(timestamp_col, datetime(null)), name = column_ifexists(name_col, ''), labels = column_ifexists(labels_col, dynamic(null)), value = column_ifexists(value_col, 0)
    | extend labels = dynamic_to_json(labels)       //  convert to string and sort by key
    | where name == metric_name and timestamp between(stime..etime)
    | project timestamp, value, name, labels
    | order by timestamp asc
    | summarize timestamp = make_list(timestamp), value=make_list(value) by name, labels
    //  KQL has native has_any(), but no native has_all(), the lines below implement has_all()
    | mv-apply x = selector_d to typeof(string) on (
      summarize countif(labels has x)
      | where countif_ == array_length(selector_d)
    )
    | project-away countif_
}
```

### <a name="usage"></a>使用方式

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', 'disk:sda1,host:aks-agentpool-88086459-vmss000001', offset=now()-datetime(2020-12-08 00:00))
| render timechart with(series=labels)
```

---

:::image type="content" source="images/series-metric-fl/disk-write-metric-10m.png" alt-text="顯示磁片寫入度量超過10分鐘的圖表" border="false":::

## <a name="example"></a>範例

下列範例不會指定選取器，因此會選取所有「寫入」度量。 這個範例假設已經安裝了函式，並使用替代的直接呼叫語法，將輸入資料表指定為第一個參數：
    
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
series_metric_fl(demo_prometheus, 'TimeStamp', 'Name', 'Labels', 'Val', 'writes', offset=now()-datetime(2020-12-08 00:00))
| render timechart with(series=labels, ysplit=axes)
```
    
:::image type="content" source="images/series-metric-fl/all-disks-write-metric-10m.png" alt-text="顯示所有磁片的磁片寫入計量超過10分鐘的圖表" border="false":::
