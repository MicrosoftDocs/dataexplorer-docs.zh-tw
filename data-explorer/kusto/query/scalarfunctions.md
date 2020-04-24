---
title: Scalar 函數 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 Scalar 函數。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: ef807c93f9ae9b125439f55f32a2bb1eb21d46b7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509549"
---
# <a name="scalar-functions"></a>純量函數

## <a name="binary-functions"></a>二進位函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[binary_and()](binary-andfunction.md)|返回兩個值之間的位和操作的結果。|
|[binary_not()](binary-notfunction.md)|返回對輸入值的一點否定。|
|[binary_or()](binary-orfunction.md)|返回兩個值的位或操作的結果。|
|[binary_shift_left()](binary-shift-leftfunction.md)|返回對一對數位的二進位移位左移操作: << n。|
|[binary_shift_right()](binary-shift-rightfunction.md)|返回對一對數位的二進位移位右操作: >> n。|
|[binary_xor()](binary-xorfunction.md)|返回兩個值的位 xor 操作的結果。|
|[bitset_count_ones()](bitset-count-onesfunction.md)|返回數位二進位表示法中的設置位數。|

## <a name="conversion-functions"></a>轉換函式

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[tobool()](toboolfunction.md)|將輸入轉換為布爾(簽名的8位)表示形式。|
|[到日期時間()](todatetimefunction.md)|將輸入轉換為日期時間標量。|
|[雙倍()/至實()](todoublefunction.md)|將輸入轉換為實際類型的值。 (雙精度)和"到"是同義詞。|
|[到字串()](tostringfunction.md)|將輸入轉換為字串表示形式。|
|[到時間跨度()](totimespanfunction.md)|將輸入轉換為時間跨度標量。|


## <a name="datetimetimespan-functions"></a>日期時間/時間跨度函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[ago()](agofunction.md)|從目前的 UTC 時鐘時間減去指定的時間範圍。|
|[datetime_add()](datetime-addfunction.md)|計算指定日期部分的新日期時間乘以指定金額,添加到指定日期時間。|
|[datetime_part()](datetime-partfunction.md)|將請求的日期部分提取為整數值。|
|[datetime_diff()](datetime-difffunction.md)|返回包含日期的年末,如果提供,則偏移量。|
|[每月的一天()](dayofmonthfunction.md)|返回表示給定月份天數的整數數位。|
|[週日()](dayofweekfunction.md)|將自上一個星期日以來的整數天數作為時間跨度返回。|
|[一天()](dayofyearfunction.md)|返回整數表示給定年份的天號。|
|[endofday()](endofdayfunction.md)|返回包含日期的一天結束,如果提供,則偏移偏移。|
|[endofmonth()](endofmonthfunction.md)|返回包含日期的月末,如果提供,則偏移量。|
|[endofweek()](endofweekfunction.md)|返回包含日期的週末,如果提供,則偏移偏移。|
|[年末()](endofyearfunction.md)|返回包含日期的年末,如果提供,則偏移量。|
|[format_datetime()](format-datetimefunction.md)|基於格式模式參數設置日期時間參數。|
|[format_timespan()](format-timespanfunction.md)|基於格式模式參數格式化格式時跨參數。|
|[取得月()](getmonthfunction.md)|從日期時間取得月份 (1-12)。|
|[取得年()](getyearfunction.md)|返回日期時間參數的年份部分。|
|[每天的小時()](hourofdayfunction.md)|返回表示給定日期的小時數的整數數位。|
|[make_datetime()](make-datetimefunction.md)|從指定的日期和時間創建日期時間標量值。|
|[make_timespan()](make-timespanfunction.md)|從指定的時間段創建時間跨度標量值。|
|[一年月()](monthofyearfunction.md)|返回整數表示給定年份的月份編號。|
|[現在()](nowfunction.md)|返回當前 UTC 時鐘時間,可選地偏移給定的時間跨度。|
|[開始一天()](startofdayfunction.md)|返回包含日期的開始日期,如果提供,則偏移偏移。|
|[月初()](startofmonthfunction.md)|返回包含日期的月的開始,如果提供,則偏移偏移。|
|[周開始()](startofweekfunction.md)|返回包含日期的周開始,如果提供,則偏移偏移。|
|[年初()](startofyearfunction.md)|返回包含日期的年初,如果提供,則偏移。|
|[到日期時間()](todatetimefunction.md)|將輸入轉換為日期時間標量。|
|[到時間跨度()](totimespanfunction.md)|將輸入轉換為時間跨度標量。|
|[一週()](weekofyearfunction.md)|返回表示周數的整數。|


## <a name="dynamicarray-functions"></a>動態/陣列功能

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[array_concat()](arrayconcatfunction.md)|將多個動態陣列與單個數位串聯。|
|[array_iif()](arrayifffunction.md)|在陣列上應用元素 iif 函數。|
|[array_index_of()](arrayindexoffunction.md)|搜索陣列以搜尋指定的項,並返回其位置。|
|[array_length()](arraylengthfunction.md)|計算動態陣列中的元素數。|
|[array_slice()](arrayslicefunction.md)|提取動態陣列的切片。|
|[array_split()](arraysplitfunction.md)|生成從輸入陣列拆分的陣列陣列。|
|[bag_keys()](bagkeysfunction.md)|枚舉動態屬性包物件中的所有根鍵。|
|[包()](packfunction.md)|從名稱和值清單中創建動態物件(屬性包)。|
|[pack_all()](packallfunction.md)|從表格運算式的所有列創建動態物件(屬性包)。|
|[pack_array()](packarrayfunction.md)|將所有輸入值打包到動態陣列中。|
|[重覆()](repeatfunction.md)|生成包含一系列相等值的動態數位。|
|[set_difference()](setdifferencefunction.md)|返回第一個陣列中但不在其他陣列中的所有不同值集的陣列。|
|[set_has_element()](sethaselementfunction.md)|確定指定的陣列是否包含指定的元素。|
|[set_intersect()](setintersectfunction.md)|返回所有陣列中所有不同值集的陣列。|
|[set_union()](setunionfunction.md)|返回任何提供陣列中所有不同值集的陣列。|
|[treepath()](treepathfunction.md)|列舉所有可識別動態物件中分葉的路徑運算式。|
|[zip()](zipfunction.md)|zip 函數接受任意數量的動態陣列,並返回一個陣列,其元素都是包含相同索引的輸入陣列元素的陣列。|


## <a name="window-scalar-functions"></a>視窗標量函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[下一個()](nextfunction.md)|對於序列化行集,根據偏移量從後一行返回指定列的值。|
|[前()](prevfunction.md)|對於序列化行集,根據偏移量從前面的行返回指定列的值。|
|[row_cumsum()](rowcumsumfunction.md)|計算列的累積總和。|
|[row_number()](rownumberfunction.md)|在序列化列集中返回行數 - 從給定索引開始或預設從 1 開始的連續數位。|

## <a name="flow-control-functions"></a>流量控制功能

|函數名稱            |描述                                             |
|-------------------------|--------------------------------------------------------|
|[toscalar()](toscalarfunction.md)|返回計算表達式的標量常量值。|

## <a name="mathematical-functions"></a>數學函式

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[abs()](abs-function.md)|計算輸入的絕對值。|
|[阿科斯()](acosfunction.md)|返回其成因為指定數位(cos()的反向操作)的角度。|
|[阿辛()](asinfunction.md)|返回正著斯為指定數位(正()的反向操作)的角度。|
|[阿坦()](atanfunction.md)|返回切線為指定數位(tan()的反向操作)的角度。|
|[阿坦2()](atan2function.md)|計算正 x 軸和從原點到點 (y, x) 的光線之間的角度(以弧度表示)。|
|[beta_cdf()](beta-cdffunction.md)|返回標準累積 Beta 分發功能。|
|[beta_inv()](beta-invfunction.md)|返回測試版累積概率測試密度函數的逆。|
|[beta_pdf()](beta-pdffunction.md)|返回概率密度測試函數。|
|[科斯()](cosfunction.md)|返回可數函數。|
|[嬰兒床()](cotfunction.md)|以弧度計算指定角度的三角共量。|
|[度()](degreesfunction.md)|使用公式度 = (180 / PI ) = 角度在弧度)將角度值以弧度轉換為度值。|
|[exp()](exp-function.md)|x 的基-e 指數函數,該函數被提升到冪 x:e_x。|
|[exp10()](exp10-function.md)|x 的基-10指數函數,該函數為 10,提升到冪 x:10°x。|
|[exp2()](exp2-function.md)|x 的基-2指數函數,該函數為 2,提升為功率 x:2+x。|
|[伽馬()](gammafunction.md)|計算伽馬函數。|
|[雜湊()](hashfunction.md)|返回輸入值的哈希值。|
|[hash_combine()](hash_combinefunction.md)|合併兩個或多個哈希值。|
|[hash_many()](hash_manyfunction.md)|返回多個值的組合哈希值。|
|[isfinite()](isfinitefunction.md)|返回輸入是否為有限值(既不是無限值也不是 NaN)。|
|[isinf()](isinffunction.md)|返回輸入是無限(正值還是負值)。|
|[isnan()](isnanfunction.md)|返回輸入是否不是數位 (NaN) 值。|
|[紀錄()](log-function.md)|返回自然對數函數。|
|[紀錄10()](log10-function.md)|重新調諧 comon(基-10)對數函數。|
|[log2()](log2-function.md)|返回基-2對數函數。|
|[loggamma()](loggammafunction.md)|計算伽馬函數絕對值的日誌。|
|[不()](notfunction.md)|反轉其布爾參數的值。|
|[pi()](pifunction.md)|返回 Pi (*) 的常量值。|
|[波()](powfunction.md)|返回提升為權力的結果。|
|[弧度()](radiansfunction.md)|使用公式弧度 = (PI / 180 ) = 度角,將角度值(以度表示為弧度)轉換為值。|
|[蘭特()](randfunction.md)|返回隨機數。|
|[範圍()](rangefunction.md)|生成包含一系列等間距值的動態數位。|
|[圓形()](roundfunction.md)|將舍入源返回到指定的精度。|
|[符號()](signfunction.md)|數字表達式的符號。|
|[辛()](sinfunction.md)|返回子周函數。|
|[平方()](sqrtfunction.md)|返回平方根函數。|
|[棕褐色()](tanfunction.md)|返回切線函數。|
|[welch_test()](welch-testfunction.md)|計算[韋爾奇測試函數](https://en.wikipedia.org/wiki/Welch%27s_t-test)的 p 值。|


## <a name="metadata-functions"></a>中繼資料函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[column_ifexists()](columnifexists.md)|將列名作為字串和預設值。 如果列存在,則返回對列的引用,否則返回預設值。|
|[current_cluster_endpoint()](current-cluster-endpoint-function.md)|返回運行查詢的當前群集。|
|[current_database()](current-database-function.md)|返回作用域中的資料庫的名稱。|
|[current_principal()](current-principalfunction.md)|返回運行此查詢的當前主體。|
|[current_principal_details()](current-principal-detailsfunction.md)|返回運行查詢的主體的詳細資訊。|
|[current_principal_is_member_of()](current-principal-ismemberoffunction.md)|檢查運行查詢的當前主體的組成員身份或主體標識。|
|[cursor_after()](cursorafterfunction.md)|用於訪問游標的上一個值之後引入的記錄。|
|[estimate_data_size()](estimate-data-sizefunction.md)|傳回表格運算式所選列的估計數據大小。|
|[extent_id()](extentidfunction.md)|返回標識當前記錄駐留在的數據分片("範圍")的唯一標識符。|
|[extent_tags()](extenttagsfunction.md)|返回包含當前記錄駐留在的數據分片("範圍")標記的動態陣列。|
|[ingestion_time()](ingestiontimefunction.md)|檢索記錄$IngestionTime隱藏日期時間列,或 null。|


## <a name="rounding-functions"></a>進位式

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[箱()](binfunction.md)|將值捨入為指定 bin 大小的整數倍數。|
|[bin_at()](binatfunction.md)|將值舍入到固定大小的"bin",並控制 bin 的起點。 (另請參閱 bin 函數。|
|[天花板()](ceilingfunction.md)|計算大於或等於指定數值表達式的最小整數。|
|[地板()](floorfunction.md)|將值捨入為指定 bin 大小的整數倍數。|


## <a name="conditional-functions"></a>條件式函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[案例()](casefunction.md)|計算謂詞清單並返回滿足謂詞的第一個結果表達式。|
|[合併()](coalescefunction.md)|計算運算式清單並返回第一個非空(或字串的非空)運算式。|
|[iif()/iff()](iiffunction.md)|計算第一個參數(謂詞),並返回第二個或第三個參數的值,具體取決於計算為 true(第二個)還是 false(第三個)。|
|[max_of()](max-offunction.md)|返回多個計算的數字運算式的最大值。|
|[min_of()](min-offunction.md)|返回多個計算的數字表達式的最小值。|

## <a name="series-element-wise-functions"></a>序列元素函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[series_add()](series-addfunction.md)|計算兩個數位系列輸入的元素級添加。|
|[series_divide()](series-dividefunction.md)|計算兩個數位系列輸入的元素劃分。|
|[series_equals()](series-equalsfunction.md)|計算兩個數位系列輸入的元素等於`==`( ) 邏輯操作。|
|[series_greater()](series-greaterfunction.md)|計算兩個數位系列輸入的元素更大`>`( ) 邏輯操作。|
|[series_greater_equals()](series-greater-equalsfunction.md)|計算兩個數位系列輸入的元級更大或等於`>=`( ) 邏輯操作。|
|[series_less()](series-lessfunction.md)|計算兩個數位系列輸入的元素減去`<`( ) 邏輯操作。|
|[series_less_equals()](series-less-equalsfunction.md)|計算兩個數位系列輸入的元素小於或等於`<=`( ) 邏輯操作。|
|[series_multiply()](series-multiplyfunction.md)|計算兩個數位系列輸入的元乘法。|
|[series_not_equals()](series-not-equalsfunction.md)|計算兩個數位系列輸入的元素不等於`!=`( ) 邏輯操作。|
|[series_subtract()](series-subtractfunction.md)|計算兩個數位系列輸入的元素減法。|

## <a name="series-processing-functions"></a>列式處理功能

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[series_decompose()](series-decomposefunction.md)|將序列分解為元件。|
|[series_decompose_anomalies()](series-decompose-anomaliesfunction.md)|根據序列分解查找序列中的異常。|
|[series_decompose_forecast()](series-decompose-forecastfunction.md)|基於序列分解的預測。|
|[series_fill_backward()](series-fill-backwardfunction.md)|對系列中缺失值執行向後填充插值。|
|[series_fill_const()](series-fill-constfunction.md)|將序列中缺少的值替換為指定的常量值。|
|[series_fill_forward()](series-fill-forwardfunction.md)|對系列中缺失值執行正向填充插值。|
|[series_fill_linear()](series-fill-linearfunction.md)|對序列中缺失值執行線性插值。|
|[series_fir()](series-firfunction.md)|在序列上應用有限脈衝回應篩選器。|
|[series_fit_2lines()](series-fit-2linesfunction.md)|在序列上應用兩個線段線性回歸,返回多個列。|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|在序列上應用兩個線段線性回歸,返回動態物件。|
|[series_fit_line()](series-fit-linefunction.md)|在序列上應用線性回歸,返回多個列。|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|在序列上應用線性回歸,返回動態物件。|
|[series_iir()](series-iirfunction.md)|在序列上應用無限脈衝回應篩選器。|
|[series_outliers()](series-outliersfunction.md)|在序列中得分異常點。|
|[series_pearson_correlation()](series-pearson-correlationfunction.md)|計算兩個系列的 Pearson 相關係數。|
|[series_periods_detect()](series-periods-detectfunction.md)|查找時間序列中存在的最重要期間。|
|[series_periods_validate()](series-periods-validatefunction.md)|檢查時間序列是否包含給定長度的週期性模式。|
|[series_seasonal()](series-seasonalfunction.md)|查找系列的季節性元件。|
|[series_stats()](series-statsfunction.md)|在多列中返回序列的統計資訊。|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|返回動態物件中序列的統計資訊。|

## <a name="string-functions"></a>字串函式

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[base64_encode_tostring()](base64_encode_tostringfunction.md)|將字串編碼為 base64 字串。|
|[base64_decode_tostring()](base64_decode_tostringfunction.md)|將 base64 字串解碼為 UTF-8 字串。|
|[base64_decode_toarray()](base64_decode_toarrayfunction.md)|將 base64 字串解碼為長值陣列。|
|[countof()](cotfunction.md)|計算子字串在字串中的出現次數。 純文字字串的相符項目可能會重疊；regex 的相符項目則不會。|
|[擷取物()](extractfunction.md)|從文字字串取得 規則運算式 的相符項目。|
|[extract_all()](extractallfunction.md)|從文字字串獲取正則表達式的所有匹配項。|
|[extractjson()](extractjsonfunction.md)|使用路徑運算式從 JSON 文字取出指定的項目。|
|[() 索引](indexoffunction.md)|函數報告輸入字串中第一次出現的指定字串的零基索引。|
|[為空()](isemptyfunction.md)|如果參數為空字串或為 null,則傳回 true。|
|[isnotempty()](isnotemptyfunction.md)|如果參數不是空字串,也不是 null,則傳回 true。|
|[不無()](isnotnullfunction.md)|如果參數不為空,則返回 true。|
|[是空()](isnullfunction.md)|計算其唯一參數並返回 bool 值,指示參數是否計算為空值。|
|[parse_csv()](parsecsvfunction.md)|拆分表示逗號分隔值的給定字串,並返回具有這些值的字串陣列。|
|[parse_ipv4()](parse-ipv4function.md)|將輸入轉換為長(已簽名的 64 位)數位表示形式。|
|[parse_json()](parsejsonfunction.md)|將字串解釋為 JSON 值),並將該值返回為動態值。|
|[parse_url()](parseurlfunction.md)|分析絕對網址 字串並傳回包含網址所有部分的動態物件。|
|[parse_urlquery()](parseurlqueryfunction.md)|分析 URL 查詢字串並返回包含查詢參數的動態物件。|
|[parse_version()](parse-versionfunction.md)|將版本的輸入字串表示形式轉換為可比較的小數位數。|
|[取代()](replacefunction.md)|用另一個字串取代所有 regex 相符項目。|
|[反向()](reversefunction.md)|函數使輸入字串反轉。|
|[分割()](splitfunction.md)|根據給定的分隔符拆分給定字串,並返回包含子字串的字串的字串陣列。|
|[斯特卡特()](strcatfunction.md)|在 1 到 64 個參數之間串聯。|
|[strcat_delim()](strcat-delimfunction.md)|在 2 到 64 個參數之間串聯,與分隔符作為第一個參數提供。|
|[strcmp()](strcmpfunction.md)|比較兩個字串。|
|[斯特倫()](strlenfunction.md)|返回輸入字串的長度(以字元表示)。|
|[strrep()](strrepfunction.md)|給定字串提供的次數(預設 - 1)的重複。|
|[子字串()](substringfunction.md)|從源字串中提取子字串,從某些索引開始到字串的末尾。|
|[至上()](toupperfunction.md)|將字串轉換為大寫。|
|[翻譯()](translatefunction.md)|將一組字元("搜索清單")替換為給定字串中的另一組字元("替換清單")。|
|[修剪()](trimfunction.md)|刪除指定正規表示式的所有前導和尾隨匹配項。|
|[trim_end()](trimendfunction.md)|刪除指定正規表示式的尾隨匹配項。|
|[trim_start()](trimstartfunction.md)|刪除指定正規表示式的前導匹配項。|
|[url_decode()](urldecodefunction.md)|該函數將編碼的 URL 轉換為常規 URL 表示形式。|
|[url_encode()](urlencodefunction.md)|該函數將輸入 URL 的字元轉換為可以通過 Internet 傳輸的格式。|

## <a name="ip-v4-functions"></a>IP v4 功能

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[ipv4_compare()](ipv4-comparefunction.md)|比較兩個 IPv4 字串。|
|[ipv4_is_match()](ipv4-is-matchfunction.md)|匹配兩個 IPv4 字串。|
|[parse_ipv4()](parse-ipv4function.md)|將輸入字串轉換為長(簽名的64位)數位表示形式。|
|[parse_ipv4_mask()](parse-ipv4-maskfunction.md)|將輸入字串和 IP 首碼遮罩為長(簽名的 64 位元)數位表示形式。|

## <a name="type-functions"></a>型別函式

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[gettype()](gettypefunction.md)|返回其單個參數的運行時類型。|


## <a name="scalar-aggregation-functions"></a>Scalar 彙總函式

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[dcount_hll()](dcount-hllfunction.md)|計算 hll 結果(由 hll 或 hll 合併生成的)的 dcount。|
|[hll_merge()](hllmergefunction.md)|合併 hll 結果(聚合版本 hll-merge()的標段版本)。|
|[percentile_tdigest()](percentile-tdigestfunction.md)|計算從 t消化結果(由 t 消化或合併-t 消化生成)的百分位數結果。|
|[percentrank_tdigest()](percentrank-tdigestfunction.md)|計算數據集中的值的百分比排名。|
|[rank_tdigest()](rank-tdigest.md)|計算集中的值的相對排名。|
|[tdigest_merge()](tdigest-mergefunction.md)|合併摘要結果(聚合版本 tdigest-merge()的標段版本)。|

## <a name="geo-spatial-functions"></a>地理空間函數

|函數名稱|描述|
|--------------------------------------------------------------------------|--------------------------------------------------------|
|[geo_distance_2points()](geo-distance-2points-function.md)|計算地球上兩個地理空間座標之間的最短距離。|
|[geo_geohash_to_central_point()](geo-geohash-to-central-point-function.md)|計算表示地理哈希矩形區域中心的地理空間座標。|
|[geo_point_in_circle()](geo-point-in-circle-function.md)|計算地理空間座標是否位於地球上的一個圓內。|
|[geo_point_to_geohash()](geo-point-to-geohash-function.md)|計算地理位置的 Geohash 字串值。|