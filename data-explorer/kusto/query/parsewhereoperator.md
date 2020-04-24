---
title: 解析位置運算子 ─ Azure 資料資源管理員 |微軟文件
description: 本文介紹了 Azure 資料資源管理器中的解析位置運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/12/2020
ms.openlocfilehash: 0e44ab83242fc8aed0e46bdab1fa5a142992bfe5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511402"
---
# <a name="parse-where-operator"></a>parse-where 運算子

評估字串運算式，並將其值剖析至一或多個計算的資料行。 結果只是成功解析的字串。
請參閱[分析](parseoperator.md)運算子,該運算元為未成功解析的字串生成 null。

```kusto
T | parse-where Text with "ActivityName=" name ", ActivityType=" type
```

**語法**

*T* `| parse-where` T `kind=regex` `flags=regex_flags`[`simple`] [ ]*Expression*`*``:`*ColumnType*`*`*ColumnName**StringConstant*表示式 ( 字串常量 欄位 ( 字串: 欄型態 ) ... `with`

**引數**

* *T*: 輸入表。

* 種類： 

    * 簡單(預設值):StringConstant 是常規字串值,匹配很嚴格,這意味著所有字串分隔符都應出現在解析的字串中,並且所有擴展列都必須與所需的類型匹配。
        
    * regex : StringConstant 可能是一個正則運算式,並且匹配很嚴格,這意味著所有字串分隔元(可以是此模式的正則運算式)都應出現在解析的字串中,並且所有擴展列都必須與所需的類型匹配。
    
    * 標誌 : 要在 regex`U`模式下`m`使用的標誌,如`s`(ungreedy)、( 多`\n`行模式`i`)、(匹配新線 )、( 不區分大小寫)等在[RE2 標誌](re2.md)中使用。
        
* *運算式*:計算到字串的運算式。

* *欄位名稱:* 要將值(從字串表達式中獲取)分配給的列的名稱。 
  
* *列類型:* 應為可選標量類型,指示要將值轉換為的類型(預設情況下為字串類型)。

**傳回**

輸入表,根據提供給運算符的列列表進行擴展。
只有成功解析的字串才會在輸出中。 與模式不匹配的字串將被篩選出。

**技巧**

* `parse-where`解析字串的方式與[解析](parseoperator.md)的方式相同,但此外,則篩選出未成功解析的字串。

* 如果要[`project`](projectoperator.md)刪除或重命名某些列,請使用。

* 在模式中使用 * 以跳過垃圾值(在字串列後無法使用)

* 解析模式可能從*列名*開始,而不僅僅是*從 StringConstant*開始。 

* 如果解析的*表示式*不是類型字串,它將轉換為類型字串。

* 如果使用正規表示式模式,則有一個選項可以添加正則表達式標誌,以便控制分析中使用的整個正則運算式。

* 在正則運算式模式下,分析將模式轉換為正則運算式並使用[RE2 語法](re2.md),以便使用內部處理的編號捕獲的組進行匹配。
  
  例如, 此解析語語 :
  
    ```kusto
    parse-where kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    內部解析將產生的正規表示式是`.*?<regex1>(.*?)<regex2>(\-\d+)`。
        
    - `*`翻譯成`.*?`。
        
    - `string`翻譯成`.*?`。
        
    - `long`翻譯成`\-\d+`。

**範例**

運算符`parse-where`通過在同`string`一運算式上`extend`使用`extract`多個 應用程式為表提供了簡化的方法。
當表具有包含`string`要分解為單個列的多個值的列(如由開發人員跟蹤("/")`printf``Console.WriteLine`語句生成的列時,這非常有用。

在下面的範例中,假設表`EventText``Traces`的欄包含窗型`Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})`的字串 。
下面的作業將用 6 欄來`resourceName`擴`totalSlices``sliceNumber`充`lockTime ``releaseTime`表`previouLockTime``Month` `Day`: 、 、 、 、 、 、 與與 。 

在這裡,很少有字串沒有完全匹配。
使用`parse`時計算的欄位會具有 null:

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
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|總切片|切片編號|鎖時間|發佈時間|預鎖時間|
|---|---|---|---|---|---|
|||||||
|||||||
|||||||
|導管計劃|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|導管計劃|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|


使用`parse-where`結果中未成功解析的字串:

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
| parse-where EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|總切片|切片編號|鎖時間|發佈時間|預鎖時間|
|---|---|---|---|---|---|
|導管計劃|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|導管計劃|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|


對於使用正規表示式標誌的正規表示式模式:

如果我們有興趣獲取 resouceName 和總切片,並且我們使用此查詢:

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

在上面的查詢中,我們沒有得到任何結果,因為預設模式區分大小寫,因此沒有成功解析任何字串。

為了得到所需的結果,我們可以運行`parse-where`不區分大小寫`i`( ) 正則表達式標誌。
請注意,將僅對 3 個字串進行成功解析,因此結果是 3 條記錄(某些總切片包含無效整數): 

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

|resourceName|總切片|
|---|---|
|導管計劃|27|
|導管計劃|27|
|導管計劃|27|


