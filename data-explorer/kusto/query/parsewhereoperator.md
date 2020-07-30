---
title: parse-where 運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的 parse 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/12/2020
ms.openlocfilehash: c2936ec7461850aaad6fdb4e9daa7624dd561c49
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346247"
---
# <a name="parse-where-operator"></a>parse-where 運算子

評估字串運算式，並將其值剖析成一或多個計算結果欄。 結果只是成功剖析的字串。 

請參閱[parse 運算子](parseoperator.md)，它會針對未成功剖析的字串產生 null。

```kusto
T | parse-where Text with "ActivityName=" name ", ActivityType=" type
```

## <a name="syntax"></a>語法

*T* `| parse-where` [ `kind=regex` [ `flags=regex_flags` ] | `simple` ] *Expression* `with` `*` （*StringConstant* *ColumnName* [ `:` *ColumnType*]） `*` .。。

## <a name="arguments"></a>引數

* *T*：輸入資料表。

* *種類*： 

    * *simple* （預設值）： StringConstant 是一般字串值，比對是嚴格的。 所有字串分隔符號都應該出現在剖析的字串中，而且所有擴充的資料行都必須符合所需的類型。
        
    * *RegEx*： StringConstant 可以是正則運算式，而相符項則是嚴格的。 所有字串分隔符號都應該出現在剖析的字串中，而且所有擴充的資料行都必須符合所需的類型。 字串分隔符號可以是此模式的 RegEx。
    
    * *flags*：要在 RegEx 模式中使用的旗標： `U` （Ungreedy）、 `m` （多行模式）、 `s` （符合新行 `\n` ）、 `i` （不區分大小寫），可以在[RE2 旗標](re2.md)中找到更多旗標。
        
* *Expression*：評估為字串的運算式。

* *ColumnName：* 指派給取自字串運算式之值的資料行名稱。 
  
* *ColumnType：* 應該是選擇性的純量類型，表示要將值轉換成的類型。 預設值為 [字串類型]。

## <a name="returns"></a>傳回

輸入資料表，會根據提供給運算子的資料行清單進行擴充。

> [!Note] 
> 只有成功剖析的字串才會在輸出中。 不符合模式的字串將會被篩選掉。

**提示**

* `parse-where`剖析字串的方式與 parse 相同，並篩選出未成功[剖析](parseoperator.md)的字串。

* 如果您也想要捨棄或重新命名某些資料行，請使用 [[專案](projectoperator.md)]。

* 在模式中使用 * 來略過垃圾的值。 這個值不能用在字串資料行之後。

* 除了*StringConstant*之外，剖析模式可能會以*ColumnName*開頭。 

* 如果剖析的*運算式*不是字串類型，它會轉換成字串類型。

* 如果使用 RegEx 模式，您可以加入 RegEx 旗標，以控制用於剖析的整個 RegEx。

* 在 RegEx 模式中，parse 會將模式轉譯成 RegEx，並使用[RE2 語法](re2.md)來進行比對，使用在內部處理的編號捕捉群組進行比對。
  
  例如，此 parse 語句：
  
    ```kusto
    parse-where kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    剖析在內部產生的 RegEx 是 `.*?<regex1>(.*?)<regex2>(\-\d+)` 。
        
    - `*`已轉譯為 `.*?` 。
        
    - `string`已轉譯為 `.*?` 。
        
    - `long`已轉譯為 `\-\d+` 。

## <a name="examples"></a>範例

`parse-where`運算子會 `extend` 在同一個運算式上使用多個應用程式，為數據表提供簡化的方式 `extract` `string` 。 當資料表有一個 `string` 資料行包含您想要細分為個別資料行的數個值時，這是最有用的。 例如，您可以分解由開發人員追蹤（" `printf` "/""）語句所產生的資料行 `Console.WriteLine` 。

### <a name="using-parse"></a>使用 `parse`

在下列範例中，資料表的資料行 `EventText` `Traces` 包含格式的字串 `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})` 。 下列作業會擴充包含六個數據行的資料表： `resourceName` 、 `totalSlices` 、 `sliceNumber` 、 `lockTime ` 、、、 `releaseTime` `previouLockTime` `Month` 和 `Day` 。 

一些字串沒有完全相符。

使用 `parse` ，計算結果欄會有 null。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=invalid_number, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=invalid_datetime, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=invalid_number, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|previousLockTime|
|---|---|---|---|---|---|
|||||||
|||||||
|||||||
|PipelineScheduler|27|20|02/17/2016 08:40:01|2016-02-17 08：40：01.0000000|2016-02-17 08：39：01.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08：41：00.0000000|2016-02-17 08：40：01.0000000|

### <a name="using-parse-where"></a>使用 `parse-where` 

使用 ' parse-where ' 會從結果中篩選出未成功剖析的字串。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=invalid_number, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=invalid_datetime, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=invalid_number, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse-where EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|previousLockTime|
|---|---|---|---|---|---|
|PipelineScheduler|27|20|02/17/2016 08:40:01|2016-02-17 08：40：01.0000000|2016-02-17 08：39：01.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08：41：00.0000000|2016-02-17 08：40：01.0000000|


### <a name="regex-mode-using-regex-flags"></a>使用 RegEx 旗標的 RegEx 模式

若要取得 totalSlices，請使用下列查詢：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=11, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=44, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse-where kind = regex EventText with * "RESOURCENAME=" resourceName "," * "totalSlices=" totalSlices:long "," *
| project resourceName, totalSlices
```

### <a name="parse-where-with-case-insensitive-regex-flag"></a>`parse-where`使用不區分大小寫的 RegEx 旗標

在上述查詢中，預設模式是區分大小寫，因此已成功剖析字串。 未取得任何結果。

若要取得所需的結果，請使用不區分 `parse-where` 大小寫（ `i` ） RegEx 旗標來執行。

只有三個字串會成功剖析，因此結果會是三筆記錄（某些 totalSlices 會保存不正確整數）。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=11, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=44, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse-where kind = regex flags=i EventText with * "RESOURCENAME=" resourceName "," * "totalSlices=" totalSlices:long "," *
| project resourceName, totalSlices
```

|resourceName|totalSlices|
|---|---|
|PipelineScheduler|27|
|PipelineScheduler|27|
|PipelineScheduler|27|
