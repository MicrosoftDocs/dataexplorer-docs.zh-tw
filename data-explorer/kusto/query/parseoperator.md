---
title: parse 運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的 parse 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 07318a64781678410374f902ff8fe5514a4bdd17
ms.sourcegitcommit: f9d3f54114fb8fab5c487b6aea9230260b85c41d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "85071902"
---
# <a name="parse-operator"></a>parse 運算子

評估字串運算式，並將其值剖析至一或多個計算的資料行。 若為未成功剖析的字串，計算結果欄會有 null。 

請參閱[parse-where](parsewhereoperator.md)運算子，它會篩選掉不成功剖析的字串。

```kusto
T | parse Text with "ActivityName=" name ", ActivityType=" type
```

**語法**

*T* `| parse` [ `kind=regex` [ `flags=regex_flags` ] | `simple` | `relaxed` ] *Expression* `with` `*` （*StringConstant* *ColumnName* [ `:` *ColumnType*]） `*` .。。

**引數**

* *T*：輸入資料表。
* 種類： 

    * simple （預設值）： StringConstant 是一般字串值，比對是嚴格的，這表示所有字串分隔符號都應該出現在剖析的字串中，而且所有擴充的資料行都必須符合所需的類型。
        
    * RegEx： StringConstant 可能是正則運算式，而比對是嚴格的，這表示所有字串分隔符號（這可以是此模式的 RegEx）應出現在剖析的字串中，而且所有擴充的資料行都必須符合所需的類型。
    
    * flags：用於 RegEx 模式的旗標，例如 `U` （Ungreedy）、 `m` （多行模式）、（ `s` 符合新行 `\n` ）、 `i` （不區分大小寫）和[RE2 旗標](re2.md)中的其他。
        
    * 寬鬆： StringConstant 是一般字串值，而且比對是寬鬆的，這表示所有字串分隔符號都應該出現在剖析的字串中，但是擴充的資料行可能會符合必要的類型部分（不符合所需類型的擴充資料行會取得 null 值）。

* *Expression*：評估為字串的運算式。

* *ColumnName：* 要指派值的資料行名稱（從字串運算式取出）。 
  
* *ColumnType：* 應該是選擇性純量類型，表示要將值轉換成的類型（根據預設，它是字串類型）。

**傳回**

輸入資料表，會根據提供給運算子的資料行清單進行擴充。

**提示**

* [`project`](projectoperator.md)如果您也想要捨棄或重新命名某些資料行，請使用。

* 在模式中使用 *，以略過垃圾值（不能用在字串資料行之後）

* 剖析模式的開頭可能是*ColumnName* ，而不只是*StringConstant*。 

* 如果剖析的*運算式*不是字串類型，它會轉換成字串類型。

* 如果使用 RegEx 模式，有一個選項可加入 RegEx 旗標，以控制用於剖析的整個 RegEx。

* 在 RegEx 模式中，parse 會將模式轉譯成 RegEx，並使用[RE2 語法](re2.md)來進行比對，以便使用在內部處理的編號捕捉群組進行比對。
  例如，這個 parse 語句：
  
    ```kusto
    parse kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    剖析在內部產生的 RegEx 是 `.*?<regex1>(.*?)<regex2>(\-\d+)` 。
        
    - `*`已轉譯為 `.*?` 。
        
    - `string`已轉譯為 `.*?` 。
        
    - `long`已轉譯為 `\-\d+` 。

**範例**

`parse`運算子會 `extend` 在同一個運算式上使用多個應用程式，為數據表提供簡化的方式 `extract` `string` 。
當資料表的 `string` 資料行包含多個您想要細分為個別資料行的值（例如，由開發人員追蹤（" `printf` "/" `Console.WriteLine` " "）語句所產生的資料行）時，這會非常有用。

在下列範例中，假設資料表的資料行 `EventText` `Traces` 包含表單的字串 `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})` 。
下列作業會擴充具有6個數據行的資料表： `resourceName` 、 `totalSlices` 、 `sliceNumber` 、、、 `lockTime ` `releaseTime` `previouLockTime` `Month` 和 `Day` 。 

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|previouLockTime|
|---|---|---|---|---|---|
|PipelineScheduler|27|15|02/17/2016 08:40:00|2016-02-17 08：40：00.0000000|2016-02-17 08：39：00.0000000|
|PipelineScheduler|27|23|02/17/2016 08:40:01|2016-02-17 08：40：01.0000000|2016-02-17 08：39：01.0000000|
|PipelineScheduler|27|20|02/17/2016 08:40:01|2016-02-17 08：40：01.0000000|2016-02-17 08：39：01.0000000|
|PipelineScheduler|27|16|02/17/2016 08:41:00|2016-02-17 08：41：00.0000000|2016-02-17 08：40：00.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08：41：00.0000000|2016-02-17 08：40：01.0000000|

若為 RegEx 模式：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse kind = regex EventText with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previouLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|sliceNumber|lockTime|releaseTime|previouLockTime|
|---|---|---|---|---|
|PipelineScheduler|15|02/17/2016 08:40:00、 |02/17/2016 08:40:00、 |2016-02-17 08：39：00.0000000|
|PipelineScheduler|23|02/17/2016 08:40:01、 |02/17/2016 08:40:01、 |2016-02-17 08：39：01.0000000|
|PipelineScheduler|20|02/17/2016 08:40:01、 |02/17/2016 08:40:01、 |2016-02-17 08：39：01.0000000|
|PipelineScheduler|16|02/17/2016 08:41:00、 |02/17/2016 08:41:00、 |2016-02-17 08：40：00.0000000|
|PipelineScheduler|22|02/17/2016 08:41:01、 |02/17/2016 08:41:00、 |2016-02-17 08：40：01.0000000|


若為使用 RegEx 旗標的 RegEx 模式：

如果我們只想要取得此「使用量」，而我們使用此查詢：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind = regex  EventText with * "resourceName=" resourceName ',' *
| project resourceName
```

|resourceName|
|---|
|PipelineScheduler，totalSlices = 27，sliceNumber = 23，lockTime = 02/17/2016 08:40:01，releaseTime = 02/17/2016 08:40:01|
|PipelineScheduler，totalSlices = 27，sliceNumber = 15，lockTime = 02/17/2016 08:40:00，releaseTime = 02/17/2016 08:40:00|
|PipelineScheduler，totalSlices = 27，sliceNumber = 20，lockTime = 02/17/2016 08:40:01，releaseTime = 02/17/2016 08:40:01|
|PipelineScheduler，totalSlices = 27，sliceNumber = 22，lockTime = 02/17/2016 08:41:01，releaseTime = 02/17/2016 08:41:00|
|PipelineScheduler，totalSlices = 27，sliceNumber = 16，lockTime = 02/17/2016 08:41:00，releaseTime = 02/17/2016 08:41:00|

但我們不會得到預期的結果，因為預設模式是 [貪婪]。
或者，即使我們有幾個記錄，其中的資料存放位置有時較低，有時會是大寫，因此我們可能會取得一些值的 null。
為了取得想要的結果，我們可以使用非貪婪（）執行此動作， `U` 並停用區分大小寫（ `i` ） RegEx 旗標：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind = regex flags = Ui EventText with * "RESOURCENAME=" resourceName ',' *
| project resourceName
```

|resourceName|
|---|
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|


如果剖析過的字串具有分行符號，您應該使用旗標 `s` 以預期的方式剖析文字：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=23\nlockTime=02/17/2016 08:40:01\nreleaseTime=02/17/2016 08:40:01\npreviousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=15\nlockTime=02/17/2016 08:40:00\nreleaseTime=02/17/2016 08:40:00\npreviousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=20\nlockTime=02/17/2016 08:40:01\nreleaseTime=02/17/2016 08:40:01\npreviousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=22\nlockTime=02/17/2016 08:41:01\nreleaseTime=02/17/2016 08:41:00\npreviousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=16\nlockTime=02/17/2016 08:41:00\nreleaseTime=02/17/2016 08:41:00\npreviousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind=regex flags=s EventText with * "resourceName=" resourceName:string "(.*?)totalSlices=" totalSlices:long "(.*?)lockTime=" lockTime:datetime "(.*?)releaseTime=" releaseTime:datetime "(.*?)previousLockTime=" previousLockTime:datetime "\\)" 
| project-away EventText
```

|resourceName|totalSlices|lockTime|releaseTime|previousLockTime|
|---|---|---|---|---|
|PipelineScheduler<br>|27|2016-02-17 08：40：00.0000000|2016-02-17 08：40：00.0000000|2016-02-17 08：39：00.0000000|
|PipelineScheduler<br>|27|2016-02-17 08：40：01.0000000|2016-02-17 08：40：01.0000000|2016-02-17 08：39：01.0000000|
|PipelineScheduler<br>|27|2016-02-17 08：40：01.0000000|2016-02-17 08：40：01.0000000|2016-02-17 08：39：01.0000000|
|PipelineScheduler<br>|27|2016-02-17 08：41：00.0000000|2016-02-17 08：41：00.0000000|2016-02-17 08：40：00.0000000|
|PipelineScheduler<br>|27|2016-02-17 08：41：01.0000000|2016-02-17 08：41：00.0000000|2016-02-17 08：40：01.0000000|


在此寬鬆模式的範例中： totalSlices 擴充資料行必須是 long 類型，但在剖析的字串中，其值為 nonValidLongValue。
releaseTime 擴充資料行有相同的問題，nonValidDateTime 值無法剖析為 datetime。
在此情況下，這兩個擴充的資料行將會得到 null 值，而另一個則會取得正確的值（例如 sliceNumber）。

下列相同查詢的 using kind = simple 會針對所有延伸資料行提供 null，因為它在擴充資料行上是嚴格的（這是寬鬆模式與簡單模式之間的差異，在寬鬆模式中，延伸資料行可以部分比對）。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=nonValidDateTime 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=nonValidDateTime, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=nonValidLongValue, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=nonValidDateTime 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=nonValidLongValue, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind=relaxed EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *
| project-away EventText
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|previouLockTime|
|---|---|---|---|---|---|
|PipelineScheduler|27|15|02/17/2016 08:40:00||2016-02-17 08：39：00.0000000|
|PipelineScheduler|27|23|02/17/2016 08:40:01||2016-02-17 08：39：01.0000000|
|PipelineScheduler||20|02/17/2016 08:40:01||2016-02-17 08：39：01.0000000|
|PipelineScheduler||16|02/17/2016 08:41:00|2016-02-17 08：41：00.0000000|2016-02-17 08：40：00.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08：41：00.0000000|2016-02-17 08：40：01.0000000|
