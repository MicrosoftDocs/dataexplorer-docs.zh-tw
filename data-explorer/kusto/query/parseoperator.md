---
title: 解析運算子 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的解析運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: beb51ac80810e5d14013e9b3571d759bb7b09125
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511504"
---
# <a name="parse-operator"></a>parse 運算子

評估字串運算式，並將其值剖析至一或多個計算的資料行。 對於未成功解析的字串,計算列將具有 null。
請參閱[解析位置](parsewhereoperator.md)運算符,該運算元篩選出未成功解析的字串。

```kusto
T | parse Text with "ActivityName=" name ", ActivityType=" type
```

**語法**

*T* `| parse` T `kind=regex` `flags=regex_flags`[`simple` ] || `relaxed` 【 運算型( string) 的物件 ( 字串常數列名稱 [ 欄型態 】) ... `*` `:` *Expression* `with` `*` *StringConstant* *ColumnName* * *

**引數**

* *T*: 輸入表。
* 種類： 

    * 簡單(預設值):StringConstant 是常規字串值,匹配很嚴格,這意味著所有字串分隔符都應出現在解析的字串中,並且所有擴展列都必須與所需的類型匹配。
        
    * regex : StringConstant 可能是一個正則運算式,並且匹配很嚴格,這意味著所有字串分隔元(可以是此模式的正則運算式)都應出現在解析的字串中,並且所有擴展列都必須與所需的類型匹配。
    
    * 標誌 : 要在 regex`U`模式下`m`使用的標誌,如`s`(ungreedy)、( 多`\n`行模式`i`)、(匹配新線 )、( 不區分大小寫)等在[RE2 標誌](re2.md)中使用。
        
    * 放鬆:StringConstant 是常規字串值,匹配是寬鬆的,這意味著所有字串分隔元都應出現在解析的字串中,但擴展列可能部分匹配所需類型(與所需類型不匹配的擴展列將使該值為空)。

* *運算式*:計算到字串的運算式。

* *欄位名稱:* 要將值(從字串表達式中獲取)分配給的列的名稱。 
  
* *列類型:* 應為可選標量類型,指示要將值轉換為的類型(預設情況下為字串類型)。

**傳回**

輸入表,根據提供給運算符的列列表進行擴展。

**技巧**

* 如果要[`project`](projectoperator.md)刪除或重命名某些列,請使用。

* 在模式中使用 * 以跳過垃圾值(在字串列後無法使用)

* 解析模式可能從*列名*開始,而不僅僅是*從 StringConstant*開始。 

* 如果解析的*表示式*不是類型字串,它將轉換為類型字串。

* 如果使用正規表示式模式,則有一個選項可以添加正則表達式標誌,以便控制分析中使用的整個正則運算式。

* 在正則運算式模式下,分析將模式轉換為正則運算式並使用[RE2 語法](re2.md),以便使用內部處理的編號捕獲的組進行匹配。
  例如, 此解析語語 :
  
    ```kusto
    parse kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    內部解析將產生的正規表示式是`.*?<regex1>(.*?)<regex2>(\-\d+)`。
        
    - `*`翻譯成`.*?`。
        
    - `string`翻譯成`.*?`。
        
    - `long`翻譯成`\-\d+`。

**範例**

運算符`parse`通過在同`string`一運算式上`extend`使用`extract`多個 應用程式為表提供了簡化的方法。
當表具有包含`string`要分解為單個列的多個值的列(如由開發人員跟蹤("/")`printf``Console.WriteLine`語句生成的列時,這非常有用。

在下面的範例中,假設表`EventText``Traces`的欄包含窗型`Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})`的字串 。
下面的作業將用 6 欄來`resourceName`擴`totalSlices``sliceNumber`充`lockTime ``releaseTime`表`previouLockTime``Month` `Day`: 、 、 、 、 、 、 與與 。 


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

|resourceName|總切片|切片編號|鎖時間|發佈時間|預鎖時間|
|---|---|---|---|---|---|
|導管計劃|27|15|02/17/2016 08:40:00|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|導管計劃|27|23|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|導管計劃|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|導管計劃|27|16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|導管計劃|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

對於正規表示式模式 :

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

|resourceName|切片編號|鎖時間|發佈時間|預鎖時間|
|---|---|---|---|---|
|導管計劃|15|02/17/2016 08:40:00, |02/17/2016 08:40:00, |2016-02-17 08:39:00.0000000|
|導管計劃|23|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|導管計劃|20|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|導管計劃|16|02/17/2016 08:41:00, |02/17/2016 08:41:00, |2016-02-17 08:40:00.0000000|
|導管計劃|22|02/17/2016 08:41:01, |02/17/2016 08:41:00, |2016-02-17 08:40:01.0000000|


對於使用正規表示式標誌的正規表示式模式:

如果我們只對獲取資源名稱感興趣,並且我們使用此查詢:

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
|管道計劃,總切片=27,切片編號=23,鎖時間=02/17/2016 08:40:01,發佈時間=02/17/2016 08:40:01|
|管道計劃,總切片=27,切片編號=15,鎖時間=02/17/2016 08:40:00,發佈時間=02/17/2016 08:40:00|
|管道計劃,總切片=27,切片編號=20,鎖時間=02/17/2016 08:40:01,發佈時間=02/17/2016 08:40:01|
|管道計劃,總切片=27,切片編號=22,鎖時間=02/17/2016 08:41:01,發佈時間=02/17/2016 08:41:00|
|管道計劃,總切片=27,切片編號=16,鎖時間=02/17/2016 08:41:00,發佈時間=02/17/2016 08:41:00|

但是,由於默認模式是貪婪的,我們沒有得到預期的結果。
或者,即使我們很少有記錄,其中資源名稱出現有時小寫有時大寫,所以我們可能會得到 null 的某些值。
為了得到想要的結果,我們可以運行這個非貪婪`U`( ) 和禁用區分`i`大小寫 ( ) regex 標誌:

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
|導管計劃|
|導管計劃|
|導管計劃|
|導管計劃|
|導管計劃|


如果解析的字串具有新線,則應使用標誌`s`按預期分析文本:

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

|resourceName|總切片|鎖時間|發佈時間|以前的鎖定時間|
|---|---|---|---|---|
|導管計劃<br>|27|2016-02-17 08:40:00.0000000|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|導管計劃<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|導管計劃<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|導管計劃<br>|27|2016-02-17 08:41:00.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|導管計劃<br>|27|2016-02-17 08:41:01.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|


在此示例中,對於放鬆模式:總切片擴展列需要類型長,但在解析的字串中,它具有非 ValidLongValue 的值。
發佈時間擴展列具有相同的問題,值非 ValidDateTime 不能解析為日期時間。
在這種情況下,這兩個擴展列將使值為空,而其他列(如sliceNumber)仍獲取正確的值。

使用種類 = 簡單,下面的同一查詢為所有擴展列提供空,因為它對擴展列很嚴格(即寬鬆模式和簡單模式之間的差異,在寬鬆模式下,擴展列可以部分匹配)。

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

|resourceName|總切片|切片編號|鎖時間|發佈時間|預鎖時間|
|---|---|---|---|---|---|
|導管計劃|27|15|02/17/2016 08:40:00||2016-02-17 08:39:00.0000000|
|導管計劃|27|23|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|導管計劃||20|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|導管計劃||16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|導管計劃|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|