---
title: 純量函數-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的純量函數。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: ccbd01faae3e71941c1bf4542f473410753155cf
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294571"
---
# <a name="scalar-functions"></a>純量函數

## <a name="binary-functions"></a>二元函式

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[binary_and()](binary-andfunction.md)|傳回兩個值之間的位 and 運算結果。|
|[binary_not()](binary-notfunction.md)|傳回輸入值的位否定。|
|[binary_or()](binary-orfunction.md)|傳回兩個值的位 or 運算結果。|
|[binary_shift_left()](binary-shift-leftfunction.md)|傳回一對數位的二元位移 left 運算： << n。|
|[binary_shift_right()](binary-shift-rightfunction.md)|傳回一對數位的二元右移 right 運算： >> n。|
|[binary_xor()](binary-xorfunction.md)|傳回兩個值之位 xor 運算的結果。|
|[bitset_count_ones()](bitset-count-onesfunction.md)|傳回數位的二進位標記法中的集合位數目。|

## <a name="conversion-functions"></a>轉換函式

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[tobool()](toboolfunction.md)|將輸入轉換成布林值（帶正負號的8位）標記法。|
|[todatetime()](todatetimefunction.md)|將輸入轉換為 datetime 純量。|
|[todouble （）/toreal （）](todoublefunction.md)|將輸入轉換成 real 類型的值。 （todouble （）和 toreal （）是同義字）。|
|[tostring （）](tostringfunction.md)|將輸入轉換為字串表示。|
|[totimespan()](totimespanfunction.md)|將輸入轉換為 timespan 純量。|

## <a name="datetimetimespan-functions"></a>DateTime/Timespan 函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[ago()](agofunction.md)|從目前的 UTC 時鐘時間減去指定的時間範圍。|
|[datetime_add()](datetime-addfunction.md)|從指定的 datepart 乘以指定的數量來計算新的日期時間，並加入至指定的日期時間。|
|[datetime_part()](datetime-partfunction.md)|將要求的日期部分解壓縮成整數值。|
|[datetime_diff()](datetime-difffunction.md)|傳回包含日期的年份結尾，如果有提供，則會位移位移。|
|[dayofmonth()](dayofmonthfunction.md)|傳回代表給定月份之日數位的整數數位。|
|[dayofweek()](dayofweekfunction.md)|傳回從上個星期日算起的整數天數，以 timespan 為單位。|
|[dayofyear()](dayofyearfunction.md)|傳回整數，代表指定年份的日數。|
|[endofday()](endofdayfunction.md)|傳回包含日期的日結束，如果有提供，則會位移位移。|
|[endofmonth()](endofmonthfunction.md)|傳回包含日期的月份結尾，如果有提供，則會位移位移。|
|[endofweek()](endofweekfunction.md)|傳回包含日期的一周結束，如果有提供，則以位移移位。|
|[endofyear()](endofyearfunction.md)|傳回包含日期的年份結尾，如果有提供，則會位移位移。|
|[format_datetime()](format-datetimefunction.md)|根據格式模式參數格式化日期時間參數。|
|[format_timespan()](format-timespanfunction.md)|根據格式模式參數格式化格式 timespan 參數。|
|[getmonth()](getmonthfunction.md)|從日期時間取得月份 (1-12)。|
|[getyear()](getyearfunction.md)|傳回 datetime 引數的年份部分。|
|[hourofday()](hourofdayfunction.md)|傳回代表指定日期之小時數的整數數位。|
|[make_datetime()](make-datetimefunction.md)|從指定的日期和時間建立日期時間純量值。|
|[make_timespan()](make-timespanfunction.md)|從指定的時間週期建立 timespan 純量值。|
|[monthofyear()](monthofyearfunction.md)|傳回整數，代表指定年份的月份數。|
|[now()](nowfunction.md)|傳回目前的 UTC 時鐘時間，選擇性地以給定的 timespan 來位移。|
|[startofday()](startofdayfunction.md)|傳回包含日期的日開始，如果有提供，則會位移位移。|
|[startofmonth()](startofmonthfunction.md)|傳回包含日期的月份開頭，如果有提供，則會位移位移。|
|[startofweek()](startofweekfunction.md)|傳回包含日期的一周開始時間，如果有提供，則會位移位移。|
|[startofyear()](startofyearfunction.md)|傳回包含日期的年份開頭，如果有提供，則會位移位移。|
|[todatetime()](todatetimefunction.md)|將輸入轉換為 datetime 純量。|
|[totimespan()](totimespanfunction.md)|將輸入轉換為 timespan 純量。|
|[weekofyear()](weekofyearfunction.md)|傳回代表周數的整數。|


## <a name="dynamicarray-functions"></a>動態/陣列函式

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[array_concat()](arrayconcatfunction.md)|將數個動態陣列串連成單一陣列。|
|[array_iif()](arrayifffunction.md)|在陣列上套用元素的 iif 函數。|
|[array_index_of()](arrayindexoffunction.md)|在陣列中搜尋指定的專案，並傳回其位置。|
|[array_length()](arraylengthfunction.md)|計算動態陣列中的元素數目。|
|[array_slice()](arrayslicefunction.md)|解壓縮動態陣列的配量。|
|[array_split()](arraysplitfunction.md)|建立從輸入陣列分割的陣列陣列。|
|[bag_keys()](bagkeysfunction.md)|列舉動態屬性包物件中的所有根機碼。|
|[pack()](packfunction.md)|從名稱和值的清單建立動態物件（屬性包）。|
|[pack_all()](packallfunction.md)|從表格式運算式的所有資料行建立動態物件（屬性包）。|
|[pack_array()](packarrayfunction.md)|將所有輸入值封裝到動態陣列中。|
|[repeat()](repeatfunction.md)|產生保存一系列相等值的動態陣列。|
|[set_difference()](setdifferencefunction.md)|傳回第一個陣列中但不在其他陣列中的所有相異值集合的陣列。|
|[set_has_element()](sethaselementfunction.md)|判斷指定的陣列是否包含指定的元素。|
|[set_intersect()](setintersectfunction.md)|傳回所有陣列中所有相異值集合的陣列。|
|[set_union()](setunionfunction.md)|傳回所有所提供陣列中所有相異值的集合陣列。|
|[treepath()](treepathfunction.md)|列舉所有可識別動態物件中分葉的路徑運算式。|
|[zip()](zipfunction.md)|Zip 函數接受任意數目的動態陣列。 傳回陣列，其專案為數組，其中的元素具有相同索引之輸入陣列的元素。|


## <a name="window-scalar-functions"></a>視窗純量函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[下一個（）](nextfunction.md)|對於序列化的資料列集，會根據位移，從較新的資料列傳回指定資料行的值。|
|[prev()](prevfunction.md)|針對序列化的資料列集，根據位移，傳回先前資料列中指定資料行的值。|
|[row_cumsum()](rowcumsumfunction.md)|計算資料行的累計總和。|
|[row_number()](rownumberfunction.md)|傳回序列化資料列集內的資料列編號-從指定索引開始的連續數位，或預設值為1。|

## <a name="flow-control-functions"></a>流量控制函式

|函數名稱            |描述                                             |
|-------------------------|--------------------------------------------------------|
|[toscalar()](toscalarfunction.md)|傳回已評估運算式的純量常數值。|

## <a name="mathematical-functions"></a>數學函式

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[abs()](abs-function.md)|計算輸入的絕對值。|
|[acos()](acosfunction.md)|傳回其餘弦為指定數位的角度（cos （）的反運算）。|
|[asin()](asinfunction.md)|傳回其正弦為指定數位的角度（sin （）的反向運算）。|
|[atan()](atanfunction.md)|傳回其正切為指定數位的角度（tan （）的反向運算）。|
|[atan2()](atan2function.md)|計算從正 X 軸到原點到點（y，x）之間的角度（以弧度為單位）。|
|[beta_cdf()](beta-cdffunction.md)|傳回標準累計搶鮮版（Beta）分佈函數。|
|[beta_inv()](beta-invfunction.md)|傳回搶鮮版（Beta）累計機率測試密度函數的反向。|
|[beta_pdf()](beta-pdffunction.md)|傳回機率密度搶鮮版（Beta）函數。|
|[cos()](cosfunction.md)|傳回余弦函數。|
|[cot()](cotfunction.md)|計算指定角度的三角餘切函數（以弧度為單位）。|
|[degrees()](degreesfunction.md)|使用公式度數 = （180/PI） * 以弧度為單位，將角度值以弧度轉換成值。|
|[exp()](exp-function.md)|X 的 base-e 指數函式，其 e 會向乘冪 x： e ^ x。|
|[exp10()](exp10-function.md)|X 的以10為底數的指數函式，其為乘冪 x： 10 ^ x 的10。|
|[exp2()](exp2-function.md)|X 的基底2指數函式，其為2的乘冪 x： 2 ^ x。|
|[gamma()](gammafunction.md)|計算 gamma 函數。|
|[hash()](hashfunction.md)|傳回輸入值的雜湊值。|
|[hash_combine()](hash_combinefunction.md)|結合兩個或多個雜湊值。|
|[hash_many()](hash_manyfunction.md)|傳回多個值的結合雜湊值。|
|[isfinite()](isfinitefunction.md)|傳回輸入是否為有限值（不是無限或 NaN）。|
|[isinf()](isinffunction.md)|傳回輸入是否為無限（正或負）值。|
|[isnan()](isnanfunction.md)|傳回輸入是否為非數位（NaN）值。|
|[log()](log-function.md)|傳回自然對數函數。|
|[log10()](log10-function.md)|傳回通用（底數為10）對數函數。|
|[log2()](log2-function.md)|傳回以2為底數的對數函數。|
|[loggamma()](loggammafunction.md)|計算 gamma 函數絕對值的記錄。|
|[not()](notfunction.md)|反轉其 bool 引數的值。|
|[pi()](pifunction.md)|傳回 Pi （π）的常數值。|
|[pow()](powfunction.md)|傳回乘冪的結果。|
|[radians()](radiansfunction.md)|使用公式弧度 = （PI/180） * 以度為單位的角度，將角度值以弧度轉換為值。|
|[rand()](randfunction.md)|傳回亂數字。|
|[範圍（）](rangefunction.md)|產生動態陣列，保存一系列同等間距的值。|
|[round()](roundfunction.md)|傳回指定之有效位數的圓角來源。|
|[sign()](signfunction.md)|數值運算式的正負號。|
|[sin()](sinfunction.md)|傳回正弦函數。|
|[sqrt()](sqrtfunction.md)|傳回平方根函數。|
|[tan()](tanfunction.md)|傳回正切函數。|
|[welch_test()](welch-testfunction.md)|計算[Welch-test 函數](https://en.wikipedia.org/wiki/Welch%27s_t-test)的 p 值。|


## <a name="metadata-functions"></a>中繼資料函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[column_ifexists()](columnifexists.md)|接受資料行名稱做為字串和預設值。 傳回資料行的參考（如果有的話），否則傳回預設值。|
|[current_cluster_endpoint()](current-cluster-endpoint-function.md)|傳回目前正在執行查詢的叢集。|
|[current_database()](current-database-function.md)|傳回範圍中的資料庫名稱。|
|[current_principal()](current-principalfunction.md)|傳回執行此查詢的目前主體。|
|[current_principal_details()](current-principal-detailsfunction.md)|傳回執行查詢之主體的詳細資料。|
|[current_principal_is_member_of()](current-principal-ismemberoffunction.md)|檢查目前執行查詢之主體的群組成員資格或主體身分識別。|
|[cursor_after()](cursorafterfunction.md)|用來存取在先前的資料指標值之後所內嵌的記錄。|
|[estimate_data_size()](estimate-data-sizefunction.md)|傳回表格式運算式所選資料行的估計資料大小。|
|[extent_id()](extentidfunction.md)|傳回唯一識別碼，識別目前記錄所在的資料分區（「範圍」）。|
|[extent_tags()](extenttagsfunction.md)|傳回動態陣列，其中包含目前記錄所在之資料分區（「範圍」）的標記。|
|[ingestion_time()](ingestiontimefunction.md)|抓取記錄的 $IngestionTime 隱藏日期時間資料行，或為 null。|


## <a name="rounding-functions"></a>進位函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[bin()](binfunction.md)|將值捨入為指定 bin 大小的整數倍數。|
|[bin_at()](binatfunction.md)|將值向下舍入固定大小的「bin」，並控制 bin 的起點。 （另請參閱 bin 函數）。|
|[ceiling()](ceilingfunction.md)|計算大於或等於指定之數值運算式的最小整數。|
|[floor()](floorfunction.md)|將值捨入為指定 bin 大小的整數倍數。|


## <a name="conditional-functions"></a>條件式函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[case()](casefunction.md)|評估述詞清單，並傳回滿足述詞的第一個結果運算式。|
|[coalesce()](coalescefunction.md)|評估運算式的清單，並傳回第一個非 null （或非空白的字串）運算式。|
|[iif （）/iff （）](iiffunction.md)|評估第一個引數（述詞），並傳回第二個或第三個引數的值，取決於述詞是否評估為 true （第二個）或 false （第三個）。|
|[max_of()](max-offunction.md)|傳回數個已評估之數值運算式的最大值。|
|[min_of()](min-offunction.md)|傳回數個已評估之數值運算式的最小值。|

## <a name="series-element-wise-functions"></a>數列元素取向函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[series_add()](series-addfunction.md)|計算兩個數值數列輸入的元素取向相加。|
|[series_divide()](series-dividefunction.md)|計算兩個數值數列輸入的元素成對除法。|
|[series_equals()](series-equalsfunction.md)|計算 `==` 兩個數值數列輸入的元素成對 equals （）邏輯運算。|
|[series_greater()](series-greaterfunction.md)|計算兩個數值數列輸入的元素取向較大（ `>` ）邏輯運算。|
|[series_greater_equals()](series-greater-equalsfunction.md)|計算兩個數值數列輸入的元素取向大於或等於（ `>=` ）邏輯運算。|
|[series_less()](series-lessfunction.md)|計算 `<` 兩個數值數列輸入的元素較少（）邏輯運算。|
|[series_less_equals()](series-less-equalsfunction.md)|計算兩個數值數列輸入的元素的小於或等於（ `<=` ）邏輯運算。|
|[series_multiply()](series-multiplyfunction.md)|計算兩個數值數列輸入的元素取向乘法。|
|[series_not_equals()](series-not-equalsfunction.md)|計算 `!=` 兩個數值數列輸入的元素非 equals （）邏輯運算。|
|[series_subtract()](series-subtractfunction.md)|計算兩個數值數列輸入的元素的減法。|

## <a name="series-processing-functions"></a>數列處理函式

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[series_decompose()](series-decomposefunction.md)|將數列分解成元件。|
|[series_decompose_anomalies()](series-decompose-anomaliesfunction.md)|根據數列分解，尋找數列中的異常。|
|[series_decompose_forecast()](series-decompose-forecastfunction.md)|根據數列分解進行預測。|
|[series_fill_backward()](series-fill-backwardfunction.md)|執行數列中遺漏值的後置填滿插補。|
|[series_fill_const()](series-fill-constfunction.md)|以指定的常數值取代數列中的遺漏值。|
|[series_fill_forward()](series-fill-forwardfunction.md)|執行序列中遺漏值的向前填滿插補。|
|[series_fill_linear()](series-fill-linearfunction.md)|執行數列中遺漏值的線性插補。|
|[series_fir()](series-firfunction.md)|在數列上套用有限的脈衝回應篩選準則。|
|[series_fit_2lines()](series-fit-2linesfunction.md)|在數列上套用兩個區段的線性回歸，並傳回多個資料行。|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|在數列上套用兩個區段的線性回歸，傳回動態物件。|
|[series_fit_line()](series-fit-linefunction.md)|在數列上套用線性回歸，並傳回多個資料行。|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|在數列上套用線性回歸，並傳回動態物件。|
|[series_iir()](series-iirfunction.md)|在數列上套用無限脈衝回應篩選準則。|
|[series_outliers()](series-outliersfunction.md)|為數列中的異常點評分。|
|[series_pearson_correlation()](series-pearson-correlationfunction.md)|計算兩個數列的皮耳森相互關聯係數。|
|[series_periods_detect()](series-periods-detectfunction.md)|尋找存在於時間序列中最重要的期間。|
|[series_periods_validate()](series-periods-validatefunction.md)|檢查時間序列是否包含指定長度的定期模式。|
|[series_seasonal()](series-seasonalfunction.md)|尋找數列的季節性元件。|
|[series_stats()](series-statsfunction.md)|傳回多個資料行中數列的統計資料。|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|傳回動態物件中數列的統計資料。|

## <a name="string-functions"></a>字串函式

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[base64_encode_tostring()](base64_encode_tostringfunction.md)|將字串編碼為 base64 字串。|
|[base64_decode_tostring()](base64_decode_tostringfunction.md)|將 base64 字串解碼為 UTF-8 字串。|
|[base64_decode_toarray()](base64_decode_toarrayfunction.md)|將 base64 字串解碼為 long 值的陣列。|
|[countof()](cotfunction.md)|計算子字串在字串中的出現次數。 純字串符合可能會重迭;RegEx 比對不符合。|
|[extract()](extractfunction.md)|從文字字串取得 規則運算式 的相符項目。|
|[extract_all()](extractallfunction.md)|從文字字串取得正則運算式的所有相符專案。|
|[extractjson()](extractjsonfunction.md)|使用路徑運算式從 JSON 文字取出指定的項目。|
|[indexof()](indexoffunction.md)|函式會報告輸入字串內指定之字串第一次出現時的所在索引（以零為基底）。|
|[isempty()](isemptyfunction.md)|如果引數為空字串或為 null，則傳回 true。|
|[isnotempty()](isnotemptyfunction.md)|如果引數不是空字串或 null，則傳回 true。|
|[isnotnull （）](isnotnullfunction.md)|如果引數不是 null，則傳回 true。|
|[isnull()](isnullfunction.md)|評估其唯一引數，並傳回布林值，指出引數是否評估為 null 值。|
|[parse_csv()](parsecsvfunction.md)|分割代表逗號分隔值的指定字串，並傳回具有這些值的字串陣列。|
|[parse_ipv4()](parse-ipv4function.md)|將輸入轉換成 long （帶正負號的64位）數位表示。|
|[parse_ipv4_mask()](parse-ipv4-maskfunction.md)|將輸入字串和 IP 首碼遮罩轉換成 long （帶正負號的64位）數位表示。|
|[parse_ipv6 （）](parse-ipv6function.md)|將 IPv6 或 IPv4 字串轉換成標準 IPv6 字串表示。|
|[parse_ipv6_mask （）](parse-ipv6-maskfunction.md)|將 IPv6 或 IPv4 字串和網路遮罩轉換成標準 IPv6 字串表示。|
|[parse_json()](parsejsonfunction.md)|將字串解讀為 JSON 值），並以動態方式傳回值。|
|[parse_url()](parseurlfunction.md)|剖析絕對 URL 字串，並傳回動態物件，其中包含 URL 的所有部分。|
|[parse_urlquery()](parseurlqueryfunction.md)|剖析 url 查詢字串，並傳回包含查詢參數的動態物件。|
|[parse_version()](parse-versionfunction.md)|將版本的輸入字串表示轉換成可比較的十進位數。|
|[replace()](replacefunction.md)|用另一個字串取代所有 regex 相符項目。|
|[reverse()](reversefunction.md)|函式會反向輸入字串。|
|[split()](splitfunction.md)|根據指定的分隔符號分割指定的字串，並傳回包含子字串的字串陣列。|
|[strcat()](strcatfunction.md)|會串連1到64個引數。|
|[strcat_delim()](strcat-delimfunction.md)|會串連2到64個引數，並以分隔符號形式提供作為第一個引數。|
|[strcmp()](strcmpfunction.md)|比較兩個字串。|
|[strlen()](strlenfunction.md)|傳回輸入字串的長度（以字元為單位）。|
|[strrep()](strrepfunction.md)|重複指定的字串所提供的次數（預設值為-1）。|
|[substring()](substringfunction.md)|從從某個索引開始到字串結尾的來源字串，將子字串解壓縮。|
|[toupper()](toupperfunction.md)|將字串轉換為大寫。|
|[translate()](translatefunction.md)|在指定的字串中，以另一組字元（' replacementList '）取代一組字元（' searchList '）。|
|[trim()](trimfunction.md)|移除指定之正則運算式的所有開頭和結尾相符專案。|
|[trim_end()](trimendfunction.md)|移除指定之正則運算式的尾端比對。|
|[trim_start()](trimstartfunction.md)|移除指定之正則運算式的前置比對。|
|[url_decode()](urldecodefunction.md)|函式會將編碼的 URL 轉換成一般 URL 標記法。|
|[url_encode()](urlencodefunction.md)|函式會將輸入 URL 的字元轉換成可透過網際網路傳輸的格式。|

## <a name="ipv4ipv6-functions"></a>IPv4/IPv6 函式

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[ipv4_compare （）](ipv4-comparefunction.md)|比較兩個 IPv4 字串。|
|[ipv4_is_match （）](ipv4-is-matchfunction.md)|符合兩個 IPv4 字串。|
|[parse_ipv4()](parse-ipv4function.md)|將輸入字串轉換為 long （帶正負號的64位）數位表示。|
|[parse_ipv4_mask()](parse-ipv4-maskfunction.md)|將輸入字串和 IP 首碼遮罩轉換成 long （帶正負號的64位）數位表示。|
|[ipv6_compare （）](ipv6-comparefunction.md)|比較兩個 IPv4 或 IPv6 字串。|
|[ipv6_is_match （）](ipv6-is-matchfunction.md)|符合兩個 IPv4 或 IPv6 字串。|
|[parse_ipv6 （）](parse-ipv6function.md)|將 IPv6 或 IPv4 字串轉換成標準 IPv6 字串表示。|
|[parse_ipv6_mask （）](parse-ipv6-maskfunction.md)|將 IPv6 或 IPv4 字串和網路遮罩轉換成標準 IPv6 字串表示。|

## <a name="type-functions"></a>型別函式

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[gettype()](gettypefunction.md)|傳回其單一引數的執行時間類型。|

## <a name="scalar-aggregation-functions"></a>純量彙總函式

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[dcount_hll()](dcount-hllfunction.md)|計算 hll 結果中的 dcount （由 hll 或 hll 所產生）。|
|[hll_merge()](hllmergefunction.md)|合併 hll 結果（純量版本的匯總版本 hll-merge （））。|
|[percentile_tdigest()](percentile-tdigestfunction.md)|計算 tdigest 結果（由 tdigest 或 merge tdigests 產生）產生的百分位數結果。|
|[percentrank_tdigest()](percentrank-tdigestfunction.md)|計算資料集內某個值的百分比等級。|
|[rank_tdigest()](rank-tdigest.md)|計算集合中某個值的相對順位。|
|[tdigest_merge()](tdigest-mergefunction.md)|合併 tdigest 結果（純量版本的匯總版本 tdigest-merge （））。|

## <a name="geospatial-functions"></a>GeoSpatial 函式

|函數名稱|描述|
|--------------------------------------------------------------------------|--------------------------------------------------------|
|[geo_distance_2points()](geo-distance-2points-function.md)|計算兩個地理空間座標在地球上的最短距離。|
|[geo_geohash_to_central_point()](geo-geohash-to-central-point-function.md)|計算代表 Geohash 矩形區域中心的地理空間座標。|
|[geo_point_in_circle()](geo-point-in-circle-function.md)|計算地理空間座標是否在地球上的圓圈內。|
|[geo_point_to_geohash()](geo-point-to-geohash-function.md)|計算地理位置的 Geohash 字串值。|