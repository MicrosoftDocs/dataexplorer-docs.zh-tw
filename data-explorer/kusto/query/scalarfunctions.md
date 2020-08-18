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
ms.openlocfilehash: 652b08afc15d1405d6b7f17523088ed0ec5bef61
ms.sourcegitcommit: 31ebf208d6bfd901f825d048ea69c9bb3d8b87af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2020
ms.locfileid: "88501615"
---
# <a name="scalar-function-types"></a>純量函式類型

## <a name="binary-functions"></a>二進位函式

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[binary_and()](binary-andfunction.md)|傳回兩個值之間的位 and 運算結果。|
|[binary_not()](binary-notfunction.md)|傳回輸入值的位否定。|
|[binary_or()](binary-orfunction.md)|傳回兩個值的位 or 運算結果。|
|[binary_shift_left()](binary-shift-leftfunction.md)|傳回一對數位的二進位左移作業： << n。|
|[binary_shift_right()](binary-shift-rightfunction.md)|傳回一對數位的二元右移運算： >> n。|
|[binary_xor()](binary-xorfunction.md)|傳回兩個值的位 xor 運算的結果。|
|[bitset_count_ones()](bitset-count-onesfunction.md)|傳回數位的二進位標記法中的設定位數目。|

## <a name="conversion-functions"></a>轉換函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[tobool()](toboolfunction.md)|將輸入轉換為布林值， (帶正負號的8位) 標記法。|
|[todatetime()](todatetimefunction.md)|將輸入轉換為 datetime 純量。|
|[todouble ( # A1/toreal ( # B3 ](todoublefunction.md)|將輸入轉換為實數值型別的值。  (todouble ( # A2 和 toreal ( # A4 是同義字。 ) |
|[tostring ( # B1 ](tostringfunction.md)|將輸入轉換成字串表示。|
|[totimespan()](totimespanfunction.md)|將輸入轉換成 timespan 純量。|

## <a name="datetimetimespan-functions"></a>DateTime/timespan 函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[ago()](agofunction.md)|從目前的 UTC 時鐘時間減去指定的時間範圍。|
|[datetime_add ( # B1 ](datetime-addfunction.md)|從指定的 datepart 乘以指定的數量來計算新的日期時間，並將其加入至指定的日期時間。|
|[datetime_part ( # B1 ](datetime-partfunction.md)|將要求的日期部分解壓縮為整數值。|
|[datetime_diff ( # B1 ](datetime-difffunction.md)|傳回包含日期的年份結尾，如有提供，則會位移位移。|
|[dayofmonth ( # B1 ](dayofmonthfunction.md)|傳回代表指定月份之日次數位的整數。|
|[dayofweek ( # B1 ](dayofweekfunction.md)|傳回自上一個星期日起算的整數天數（以 timespan 為單位）。|
|[dayofyear ( # B1 ](dayofyearfunction.md)|傳回整數值，代表指定年份的日數。|
|[endofday()](endofdayfunction.md)|傳回包含日期的日結尾，如有提供，則會位移位移。|
|[endofmonth()](endofmonthfunction.md)|傳回包含日期的月份結尾，如有提供，則會位移位移。|
|[endofweek()](endofweekfunction.md)|傳回包含日期的周結束，如有提供，則會位移位移。|
|[endofyear()](endofyearfunction.md)|傳回包含日期的年份結尾，如有提供，則會位移位移。|
|[format_datetime()](format-datetimefunction.md)|根據格式模式參數來格式化 datetime 參數。|
|[format_timespan()](format-timespanfunction.md)|根據格式模式參數來格式化格式 timespan 參數。|
|[getmonth()](getmonthfunction.md)|從日期時間取得月份 (1-12)。|
|[getyear()](getyearfunction.md)|傳回 datetime 引數的年份部分。|
|[hourofday ( # B1 ](hourofdayfunction.md)|傳回代表指定日期之小時數的整數數位。|
|[make_datetime()](make-datetimefunction.md)|從指定的日期和時間建立 datetime 純量值。|
|[make_timespan()](make-timespanfunction.md)|從指定的時段建立 timespan 純量值。|
|[monthofyear()](monthofyearfunction.md)|傳回整數值，代表指定年份的月數。|
|[now()](nowfunction.md)|傳回目前的 UTC 時鐘時間，選擇性地以指定的 timespan 為位移。|
|[startofday()](startofdayfunction.md)|傳回包含日期的一天開始時間，如果有提供，則以位移位移。|
|[startofmonth()](startofmonthfunction.md)|傳回包含日期的月份開頭，如有提供，則會位移位移。|
|[startofweek()](startofweekfunction.md)|傳回包含日期的周次開始，如有提供，則會位移位移。|
|[startofyear()](startofyearfunction.md)|傳回包含日期的年份開頭，如有提供，則會位移位移。|
|[todatetime()](todatetimefunction.md)|將輸入轉換為 datetime 純量。|
|[totimespan()](totimespanfunction.md)|將輸入轉換成 timespan 純量。|
|[unixtime_microseconds_todatetime()](unixtime-microseconds-todatetimefunction.md)|將 unix-epoch 微秒轉換成 UTC 日期時間。|
|[unixtime_milliseconds_todatetime()](unixtime-milliseconds-todatetimefunction.md)|將 unix-epoch 毫秒轉換成 UTC 日期時間。|
|[unixtime_nanoseconds_todatetime()](unixtime-nanoseconds-todatetimefunction.md)|將 unix-epoch 毫微秒轉換成 UTC 日期時間。|
|[unixtime_seconds_todatetime()](unixtime-seconds-todatetimefunction.md)|將 unix-epoch 秒轉換為 UTC 日期時間。|
|[weekofyear()](weekofyearfunction.md)|傳回代表周數的整數。|


## <a name="dynamicarray-functions"></a>動態/陣列函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[array_concat ( # B1 ](arrayconcatfunction.md)|將許多動態陣列串連到單一陣列。|
|[array_iif()](arrayifffunction.md)|在陣列上套用元素的 iif 函數。|
|[array_index_of()](arrayindexoffunction.md)|在陣列中搜尋指定的專案，並傳回其位置。|
|[array_length ( # B1 ](arraylengthfunction.md)|計算動態陣列中的元素數目。|
|[array_slice ( # B1 ](arrayslicefunction.md)|解壓縮動態陣列的磁區。|
|[array_split()](arraysplitfunction.md)|建立從輸入陣列分割之陣列的陣列。|
|[bag_keys()](bagkeysfunction.md)|列舉動態屬性包物件中的所有根金鑰。|
|[pack()](packfunction.md)|從名稱和值清單中建立動態物件 (屬性包) 。|
|[pack_all()](packallfunction.md)|從表格式運算式的所有資料行建立動態物件 (屬性包) 。|
|[pack_array()](packarrayfunction.md)|將所有輸入值封裝至動態陣列中。|
|[repeat()](repeatfunction.md)|產生包含一系列相等值的動態陣列。|
|[set_difference ( # B1 ](setdifferencefunction.md)|傳回第一個陣列中但不在其他陣列中之所有相異值集合的陣列。|
|[set_has_element()](sethaselementfunction.md)|判斷指定的陣列是否包含指定的元素。|
|[set_intersect()](setintersectfunction.md)|傳回所有陣列中所有相異值集合的陣列。|
|[set_union ( # B1 ](setunionfunction.md)|傳回所有提供的陣列中的所有相異值集合的陣列。|
|[treepath()](treepathfunction.md)|列舉所有可識別動態物件中分葉的路徑運算式。|
|[zip()](zipfunction.md)|Zip 函數接受任意數目的動態陣列。 傳回陣列，其專案為具有相同索引之輸入陣列元素的陣列。|

## <a name="window-scalar-functions"></a>視窗純量函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[next()](nextfunction.md)|針對已序列化的資料列集，根據位移，傳回之後資料列中所指定資料行的值。|
|[prev()](prevfunction.md)|針對已序列化的資料列集，根據位移傳回先前資料列中所指定資料行的值。|
|[row_cumsum()](rowcumsumfunction.md)|計算資料行的累計總和。|
|[row_number()](rownumberfunction.md)|從指定的索引開始，傳回序列化資料列集中的資料列編號，或預設為1。|

## <a name="flow-control-functions"></a>流程式控制制函數

|函數名稱            |描述                                             |
|-------------------------|--------------------------------------------------------|
|[toscalar()](toscalarfunction.md)|傳回評估運算式的純量常數值。|

## <a name="mathematical-functions"></a>數學函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[abs ( # B1 ](abs-function.md)|計算輸入的絕對值。|
|[acos ( # B1 ](acosfunction.md)|傳回角度，其餘弦值為 ( ( # A2 # A3 之 cos 反向運算的指定數位。|
|[asin ( # B1 ](asinfunction.md)|傳回其正弦值為指定數位的角度， (sin ( # A2 # A3 的反向運算。|
|[atan ( # B1 ](atanfunction.md)|傳回正切函數為指定數位的角度， (tan ( # A2 # A3 的反向運算。|
|[atan2()](atan2function.md)|將正 X 軸和光線之間的角度（以弧度為單位）從原點算到 (y x) 的點。|
|[beta_cdf()](beta-cdffunction.md)|傳回標準的累計 Beta 分佈函數。|
|[beta_inv()](beta-invfunction.md)|傳回搶鮮版（Beta）累計機率 Beta 密度函式的反向。|
|[beta_pdf()](beta-pdffunction.md)|傳回機率密度搶鮮版（Beta）函數。|
|[cos ( # B1 ](cosfunction.md)|傳回余弦函數。|
|[cot ( # B1 ](cotfunction.md)|計算指定角度的三角餘切（以弧度為單位）。|
|[度數 ( # B1 ](degreesfunction.md)|將弧度的角度值以度數轉換成值，並使用公式度數 = (180/PI) * 以弧度為單位的角度。|
|[exp ( # B1 ](exp-function.md)|X 的 e-e 指數函式，此函式會對 power x： e ^ x 引發。|
|[exp10()](exp10-function.md)|X 的以10為底數的指數函式，其為10到乘冪 x： 10 ^ x。|
|[exp2()](exp2-function.md)|X 的以2為底數的指數函式，其為2到乘冪 x： 2 ^ x。|
|[gamma()](gammafunction.md)|計算 gamma 函數。|
|[isfinite()](isfinitefunction.md)|傳回輸入是否為有限值， (不是無限或 NaN) 。|
|[isinf()](isinffunction.md)|傳回輸入是否為無限 (正數或負) 值。|
|[isnan()](isnanfunction.md)|傳回輸入是否不是數位 (NaN) 值。|
|[log ( # B1 ](log-function.md)|傳回自然對數函數。|
|[log10 ( # B1 ](log10-function.md)|傳回 common (base-10) 對數函數。|
|[log2()](log2-function.md)|傳回以2為底數的對數函數。|
|[loggamma()](loggammafunction.md)|計算 gamma 函數絕對值的記錄。|
|[不 ( # B1 ](notfunction.md)|反轉其 bool 引數的值。|
|[pi ( # B1 ](pifunction.md)|傳回 Pi 的常數值 (π) 。|
|[pow ( # B1 ](powfunction.md)|傳回乘冪的結果。|
|[弧度 ( # B1 ](radiansfunction.md)|使用公式弧度 = (PI/180) * 角度為單位，將以度為單位的角度值轉換成弧度值。|
|[rand ( # B1 ](randfunction.md)|傳回亂數字。|
|[範圍 ( # B1 ](rangefunction.md)|產生包含一系列平均間距值的動態陣列。|
|[四捨五入 ( # B1 ](roundfunction.md)|將圓角來源傳回至指定的有效位數。|
|[sign ( # B1 ](signfunction.md)|數值運算式的正負號。|
|[sin ( # B1 ](sinfunction.md)|傳回正弦函數。|
|[sqrt ( # B1 ](sqrtfunction.md)|傳回平方根函數。|
|[tan ( # B1 ](tanfunction.md)|傳回正切函數。|
|[welch_test()](welch-testfunction.md)|計算 [Welch-測試函數](https://en.wikipedia.org/wiki/Welch%27s_t-test)的 p 值。|

## <a name="metadata-functions"></a>中繼資料函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[column_ifexists()](columnifexists.md)|使用資料行名稱做為字串和預設值。 傳回資料行的參考（如果有的話），否則傳回預設值。|
|[current_cluster_endpoint()](current-cluster-endpoint-function.md)|傳回目前執行查詢的叢集。|
|[current_database()](current-database-function.md)|傳回範圍中資料庫的名稱。|
|[current_principal()](current-principalfunction.md)|傳回執行此查詢的目前主體。|
|[current_principal_details()](current-principal-detailsfunction.md)|傳回執行查詢之主體的詳細資料。|
|[current_principal_is_member_of()](current-principal-ismemberoffunction.md)|檢查目前正在執行查詢之主體的群組成員資格或主體身分識別。|
|[cursor_after()](cursorafterfunction.md)|用來存取在先前的資料指標值之後所內嵌的記錄。|
|[estimate_data_size()](estimate-data-sizefunction.md)|傳回表格式運算式所選資料行的預估資料大小。|
|[extent_id()](extentidfunction.md)|傳回唯一識別碼，識別目前記錄所在 ( 「範圍」 ) 的資料分區。|
|[extent_tags()](extenttagsfunction.md)|傳回動態陣列，其中含有目前記錄所在 ( 「範圍」 ) 資料分區的標記。|
|[ingestion_time ( # B1 ](ingestiontimefunction.md)|抓取記錄的 $IngestionTime 隱藏日期時間資料行，或 null。|

## <a name="rounding-functions"></a>進位函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[bin()](binfunction.md)|將值捨入為指定 bin 大小的整數倍數。|
|[bin_at()](binatfunction.md)|將值向下舍入為固定大小的「bin」，可控制 bin 的起點。  (另請參閱 bin 函數。 ) |
|[上限 ( # B1 ](ceilingfunction.md)|計算大於或等於指定之數值運算式的最小整數。|
|[樓層 ( # B1 ](floorfunction.md)|將值捨入為指定 bin 大小的整數倍數。|

## <a name="conditional-functions"></a>條件式函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[case()](casefunction.md)|評估述詞的清單，並傳回滿足其述詞的第一個結果運算式。|
|[coalesce()](coalescefunction.md)|評估運算式的清單，並傳回字串) 運算式的第一個非 null (或非空白的。|
|[iif ( # A1/如果 ( # B3 ](iiffunction.md)|評估 (述詞) 的第一個引數，並傳回第二個或第三個引數的值，取決於述詞是否評估為 true (第二個) 或 false (第三) 。|
|[max_of()](max-offunction.md)|傳回數個評估數值運算式的最大值。|
|[min_of()](min-offunction.md)|傳回數個評估數值運算式的最小值。|

## <a name="series-element-wise-functions"></a>數列元素取向函式

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[series_add()](series-addfunction.md)|計算兩個數值數列輸入的元素的加法。|
|[series_divide()](series-dividefunction.md)|計算兩個數值數列輸入的元素面向除法。|
|[series_equals()](series-equalsfunction.md)|計算 `==` 兩個數值序列輸入的元素取向等於 () 邏輯運算。|
|[series_greater()](series-greaterfunction.md)|計算 `>` 兩個數值序列輸入的元素更高 () 邏輯運算。|
|[series_greater_equals()](series-greater-equalsfunction.md)|計算 `>=` 兩個數值序列輸入的 () 邏輯運算的最高或等於元素。|
|[series_less()](series-lessfunction.md)|計算 `<` 兩個數值序列輸入的 () 邏輯運算較少的元素。|
|[series_less_equals()](series-less-equalsfunction.md)|計算 `<=` 兩個數值序列輸入的 () 邏輯運算的較小或等於元素。|
|[series_multiply()](series-multiplyfunction.md)|計算兩個數值數列輸入的元素成對乘法。|
|[series_not_equals()](series-not-equalsfunction.md)|計算 `!=` 兩個數值序列輸入的 () 邏輯運算的不等於元素。|
|[series_subtract()](series-subtractfunction.md)|計算兩個數值數列輸入的元素成對減法。|

## <a name="series-processing-functions"></a>數列處理函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[series_decompose()](series-decomposefunction.md)|會將數列分解為元件。|
|[series_decompose_anomalies()](series-decompose-anomaliesfunction.md)|根據序列分解來尋找數列中的異常。|
|[series_decompose_forecast()](series-decompose-forecastfunction.md)|根據數列分解進行預測。|
|[series_fill_backward()](series-fill-backwardfunction.md)|在數列中執行遺漏值的後置填滿插補。|
|[series_fill_const()](series-fill-constfunction.md)|以指定的常數值取代數列中的遺漏值。|
|[series_fill_forward()](series-fill-forwardfunction.md)|在數列中執行遺漏值的向前填滿插補。|
|[series_fill_linear()](series-fill-linearfunction.md)|在數列中執行遺漏值的線性插補。|
|[series_fft ( # B1 ](series-fft-function.md)|在數列上 (FFT) 套用 Fast 傅立葉轉換。|
|[series_fir()](series-firfunction.md)|在數列上套用有限的脈衝回應篩選。|
|[series_fit_2lines()](series-fit-2linesfunction.md)|在數列上套用兩個區段線性回歸，並傳回多個資料行。|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|在數列上套用兩個區段線性回歸，並傳回動態物件。|
|[series_fit_line()](series-fit-linefunction.md)|對數列套用線性回歸，並傳回多個資料行。|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|對數列套用線性回歸，並傳回動態物件。|
|[series_ifft ( # B1 ](series-ifft-function.md)|在數列上 (IFFT) 套用反向的傅立葉轉換。|
|[series_iir()](series-iirfunction.md)|在數列上套用無限脈衝回應篩選。|
|[series_outliers()](series-outliersfunction.md)|針對數列中的異常點進行評分。|
|[series_pearson_correlation()](series-pearson-correlationfunction.md)|計算兩個數列的皮耳森相互關聯係數。|
|[series_periods_detect()](series-periods-detectfunction.md)|尋找存在於時間序列中的最大週期。|
|[series_periods_validate()](series-periods-validatefunction.md)|檢查時間序列是否包含指定長度的定期模式。|
|[series_seasonal()](series-seasonalfunction.md)|尋找數列的季節性元件。|
|[series_stats()](series-statsfunction.md)|傳回多個資料行中數列的統計資料。|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|傳回動態物件中數列的統計資料。|

## <a name="string-functions"></a>字串函數

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[base64_encode_tostring()](base64_encode_tostringfunction.md)|將字串編碼為 base64 字串。|
|[base64_decode_tostring()](base64_decode_tostringfunction.md)|將 base64 字串解碼為 UTF-8 字串。|
|[base64_decode_toarray()](base64_decode_toarrayfunction.md)|將 base64 字串解碼為 long 值陣列。|
|[countof()](cotfunction.md)|計算子字串在字串中的出現次數。 純字串相符專案可能會重迭;RegEx 比對不相符。|
|[extract()](extractfunction.md)|從文字字串取得 規則運算式 的相符項目。|
|[extract_all()](extractallfunction.md)|從文字字串取得正則運算式的所有符合專案。|
|[extractjson()](extractjsonfunction.md)|使用路徑運算式從 JSON 文字取出指定的項目。|
|[indexof ( # B1 ](indexoffunction.md)|函數會報告輸入字串內，第一次出現指定字串之以零為起始的索引。|
|[isempty()](isemptyfunction.md)|如果引數為空字串或為 null，則傳回 true。|
|[isnotempty()](isnotemptyfunction.md)|如果引數不是空字串或 null，則傳回 true。|
|[isnotnull()](isnotnullfunction.md)|如果引數不是 null，則傳回 true。|
|[isnull ( # B1 ](isnullfunction.md)|評估其唯一的引數，並傳回布林值，指出引數是否評估為 null 值。|
|[parse_command_line()](parse-command-line.md)|剖析 Unicode 命令列字串，並傳回命令列引數的陣列。|
|[parse_csv()](parsecsvfunction.md)|分割表示逗點分隔值的指定字串，並傳回具有這些值的字串陣列。|
|[parse_ipv4()](parse-ipv4function.md)|將輸入轉換為 long (帶正負號的64位) 數位標記法。|
|[parse_ipv4_mask()](parse-ipv4-maskfunction.md)|將輸入字串和 IP 首碼遮罩轉換為 long (簽署的64位) 數位標記法。|
|[parse_ipv6()](parse-ipv6function.md)|將 IPv6 或 IPv4 字串轉換成標準 IPv6 字串表示。|
|[parse_ipv6_mask()](parse-ipv6-maskfunction.md)|將 IPv6 或 IPv4 字串和網路遮罩轉換成標準 IPv6 字串表示。|
|[parse_json()](parsejsonfunction.md)|) 將字串解讀為 JSON 值，並以動態形式傳回值。|
|[parse_url()](parseurlfunction.md)|剖析絕對 URL 字串，並傳回動態物件，其中包含 URL 的所有部分。|
|[parse_urlquery()](parseurlqueryfunction.md)|剖析 url 查詢字串，並傳回包含查詢參數的動態物件。|
|[parse_version()](parse-versionfunction.md)|將版本的輸入字串表示轉換成可比較的十進位數。|
|[取代 ( # B1 ](replacefunction.md)|用另一個字串取代所有 regex 相符項目。|
|[反向 ( # B1 ](reversefunction.md)|函數會反轉輸入字串。|
|[分割 ( # B1 ](splitfunction.md)|根據指定的分隔符號分割指定的字串，並傳回包含子字串的字串陣列。|
|[strcat()](strcatfunction.md)|串連1和64引數。|
|[strcat_delim()](strcat-delimfunction.md)|使用分隔符號（以第一個引數提供）來串連2和64引數。|
|[strcmp()](strcmpfunction.md)|比較兩個字串。|
|[strlen()](strlenfunction.md)|傳回輸入字串的長度（以字元為單位）。|
|[strrep()](strrepfunction.md)|重複指定的字串 (預設值-1) 的次數。|
|[子字串 ( # B1 ](substringfunction.md)|從從某個索引開始到字串結尾的來源字串解壓縮子字串。|
|[toupper ( # B1 ](toupperfunction.md)|將字串轉換為大寫。|
|[translate()](translatefunction.md)|以指定字串中 ( ' replacementList ' ) 的另一組字元來取代一組字元 ( ' searchList ' ) 。|
|[trim ( # B1 ](trimfunction.md)|移除指定之正則運算式的所有開頭和尾端相符專案。|
|[trim_end()](trimendfunction.md)|移除指定之正則運算式的尾端相符項。|
|[trim_start()](trimstartfunction.md)|移除指定之正則運算式的前置比對。|
|[url_decode()](urldecodefunction.md)|函式會將編碼的 URL 轉換成一般的 URL 標記法。|
|[url_encode()](urlencodefunction.md)|函數會將輸入 URL 的字元轉換成可透過網際網路傳輸的格式。|

## <a name="ipv4ipv6-functions"></a>IPv4/IPv6 功能

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[ipv4_compare()](ipv4-comparefunction.md)|比較兩個 IPv4 字串。|
|[ipv4_is_match()](ipv4-is-matchfunction.md)|符合兩個 IPv4 字串。|
|[parse_ipv4()](parse-ipv4function.md)|將輸入字串轉換為 long (簽署的64位) 數位標記法。|
|[parse_ipv4_mask()](parse-ipv4-maskfunction.md)|將輸入字串和 IP 首碼遮罩轉換為 long (簽署的64位) 數位標記法。|
|[ipv6_compare()](ipv6-comparefunction.md)|比較兩個 IPv4 或 IPv6 字串。|
|[ipv6_is_match()](ipv6-is-matchfunction.md)|符合兩個 IPv4 或 IPv6 字串。|
|[parse_ipv6()](parse-ipv6function.md)|將 IPv6 或 IPv4 字串轉換成標準 IPv6 字串表示。|
|[parse_ipv6_mask()](parse-ipv6-maskfunction.md)|將 IPv6 或 IPv4 字串和網路遮罩轉換成標準 IPv6 字串表示。|
|[format_ipv4()](format-ipv4-function.md)|使用網路遮罩來剖析輸入，並傳回代表 IPv4 位址的字串。|
|[format_ipv4_mask()](format-ipv4-mask-function.md)|使用網路遮罩來剖析輸入，並傳回以 CIDR 標記法表示的 IPv4 位址字串。|

## <a name="type-functions"></a>類型函式

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[gettype()](gettypefunction.md)|傳回其單一引數的執行時間型別。|

## <a name="scalar-aggregation-functions"></a>純量彙總函式

|函數名稱     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[dcount_hll()](dcount-hllfunction.md)|從 hll 結果計算 dcount， (由 hll 或 hll 合併) 所產生。|
|[hll_merge()](hllmergefunction.md)|合併 hll 結果 (純量版本的匯總版本 hll-merge ( # A2 # A3。|
|[percentile_tdigest()](percentile-tdigestfunction.md)|從 tdigest 結果計算百分位數結果， (由 tdigest 或 merge tdigests) 所產生。|
|[percentrank_tdigest()](percentrank-tdigestfunction.md)|計算資料集中值的百分比排名。|
|[rank_tdigest()](rank-tdigest.md)|計算集合中值的相對順位。|
|[tdigest_merge()](tdigest-mergefunction.md)|合併 tdigest 結果 (純量版本的匯總版本 tdigest-merge ( # A2 # A3。|

## <a name="geospatial-functions"></a>GeoSpatial 函式

|函數名稱|描述|
|--------------------------------------------------------------------------|--------------------------------------------------------|
|[geo_distance_2points()](geo-distance-2points-function.md)|計算地球上兩個地理空間座標之間的最短距離。|
|[geo_distance_point_to_line()](geo-distance-point-to-line-function.md)|計算地球上座標和線條之間的最短距離。|
|[geo_point_in_circle()](geo-point-in-circle-function.md)|計算地理空間座標是否位於地球的圓形內。|
|[geo_point_in_polygon()](geo-point-in-polygon-function.md)|計算地理空間座標是否在地球的多邊形或 multipolygon 內。|
|[geo_point_to_geohash()](geo-point-to-geohash-function.md)|計算地理位置的 Geohash 字串值。|
|[geo_geohash_to_central_point()](geo-geohash-to-central-point-function.md)|計算代表 Geohash 矩形區域中心的地理空間座標。|
|[geo_point_to_s2cell()](geo-point-to-s2cell-function.md)|計算地理位置的 S2 儲存格權杖字串值。|
|[geo_s2cell_to_central_point()](geo-s2cell-to-central-point-function.md)|計算代表 S2 資料格中心的地理空間座標。|
|[geo_polygon_to_s2cells()](geo-polygon-to-s2cells-function.md)|計算在地球上涵蓋多邊形或 multipolygon 的 S2 資料格標記。 實用的地理空間聯結工具。|
|[geo_line_densify()](geo-line-densify-function.md)|藉由新增中繼點將平面線條邊緣轉換成 geodesics。|
|[geo_polygon_densify()](geo-polygon-densify-function.md)|藉由新增中繼點，將多邊形或 multipolygon 平面邊緣轉換成 geodesics。|

## <a name="hash-functions"></a>雜湊函數

|函數名稱|描述|
|--------------------------------------------------------------------------|--------------------------------------------------------|
|[雜湊 ( # B1 ](hashfunction.md)|傳回輸入值的雜湊值。|
|[hash_combine()](hash_combinefunction.md)|結合兩個或多個雜湊值。|
|[hash_many()](hash_manyfunction.md)|傳回多個值的組合雜湊值。|
|[hash_md5()](md5hashfunction.md)|傳回輸入值的 MD5 雜湊值。|
|[hash_sha256()](sha256hashfunction.md)|傳回輸入值的 SHA256 雜湊值。|
