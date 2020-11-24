---
title: ipv4_lookup 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中 ipv4_lookup 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/22/2020
ms.openlocfilehash: ec826e7d16e575fa4904aba93575ab7e605d2b18
ms.sourcegitcommit: 3af95ea6a6746441ac71b1a217bbb02ee23d5f28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511141"
---
# <a name="ipv4_lookup-plugin"></a>ipv4_lookup 外掛程式

`ipv4_lookup`外掛程式會在查閱資料表中查閱 IPv4 值，並傳回具有相符值的資料列。

```kusto
T | evaluate ipv4_lookup(LookupTable, SourceIPv4Key, LookupKey)

T | evaluate ipv4_lookup(LookupTable, SourceIPv4Key, LookupKey, return_unmatched = true)
```

## <a name="syntax"></a>語法

*T* `|` `evaluate` `ipv4_lookup(` *LookupTable* `,` *SourceIPv4Key* `,` *LookupKey* [ `,` *return_unmatched* ] `)`

## <a name="arguments"></a>引數

* *T*：表格式輸入，其資料行 *SourceIPv4Key* 將用於 IPv4 比對。
* *LookupTable*：具有 ipv4 查閱資料的資料表或表格式運算式，其資料行 *LookupKey* 將用於 ipv4 比對。 您可以使用 [IP 首碼標記法](#ip-prefix-notation)來遮罩 IPv4 值。
* *SourceIPv4Key*：包含要在 *LookupTable* 中查閱之 IPv4 字串的 *T* 資料行。 您可以使用 [IP 首碼標記法](#ip-prefix-notation)來遮罩 IPv4 值。
* *LookupKey*：具有符合每個 *SourceIPv4Key* 值之 IPv4 字串的 *LookupTable* 資料行。
* *return_unmatched*：布林值旗標，這個旗標會定義結果是否應包含所有或僅限相符的資料列 (預設： `false` -僅符合傳回) 的相符資料列。

### <a name="ip-prefix-notation"></a>IP 首碼標記法
 
您可以 `IP-prefix notation` 使用斜線 () 字元來定義 IP 位址 `/` 。
斜線 () 左邊的 IP 位址 `/` 是基底 IP 位址。  (斜線的右邊 (1 到 32) `/`) 是網路遮罩中連續1位的數目。 

例如，192.168.2.0/24 會有相關聯的 net/subnetmask，其中包含以點分隔的十進位格式的24個連續位或255.255.255.0。

## <a name="returns"></a>傳回

`ipv4_lookup`外掛程式會根據 IPv4 索引鍵傳回聯結 (查閱) 的結果。 資料表的架構是來源資料表和查閱資料表的聯集，類似于[ `lookup` 運算子](lookupoperator.md)的結果。

如果 *return_unmatched* 引數設定為 `true` ，則產生的資料表將會包含相符和不相符的資料列， (填滿) 的 null。

如果 *return_unmatched* 引數設定為 `false` ，或省略 (使用) 的預設值 `false` ，則產生的資料表會有與結果相符的記錄數目。 相較于執行，此查閱的變化效能較佳 `return_unmatched=true` 。

> [!NOTE]
> * 此外掛程式涵蓋以 IPv4 為基礎的聯結案例，假設有一個較小的查閱資料表大小 (100K 200K 資料列) ，而輸入資料表則選擇性地擁有較大的大小。
> * 外掛程式的效能將取決於查閱和資料來源資料表的大小、資料行數目和相符記錄的數目。

## <a name="examples"></a>範例

### <a name="ipv4-lookup---matching-rows-only"></a>僅限 IPv4 查閱相符的資料列

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// IP lookup table: IP_Data
// Partial data from: https://raw.githubusercontent.com/datasets/geoip2-ipv4/master/data/geoip2-ipv4.csv
let IP_Data = datatable(network:string, continent_code:string ,continent_name:string, country_iso_code:string, country_name:string)
[
  "111.68.128.0/17","AS","Asia","JP","Japan",
  "5.8.0.0/19","EU","Europe","RU","Russia",
  "223.255.254.0/24","AS","Asia","SG","Singapore",
  "46.36.200.51/32","OC","Oceania","CK","Cook Islands",
  "2.20.183.0/24","EU","Europe","GB","United Kingdom",
];
let IPs = datatable(ip:string)
[
  '2.20.183.12',   // United Kingdom
  '5.8.1.2',       // Russia
  '192.165.12.17', // Unknown
];
IPs
| evaluate ipv4_lookup(IP_Data, ip, network)
```

|ip|network|continent_code|continent_name|country_iso_code|country_name|
|---|---|---|---|---|---|
|2.20.183.12|2.20.183.0/24|EU|歐洲|GB|United Kingdom|
|5.8.1.2|5.8.0.0/19|EU|歐洲|RU|俄羅斯|

### <a name="ipv4-lookup---return-both-matching-and-non-matching-rows"></a>IPv4 查閱-傳回相符和不相符的資料列

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// IP lookup table: IP_Data
// Partial data from: 
// https://raw.githubusercontent.com/datasets/geoip2-ipv4/master/data/geoip2-ipv4.csv
let IP_Data = datatable(network:string,continent_code:string ,continent_name:string ,country_iso_code:string ,country_name:string )
[
    "111.68.128.0/17","AS","Asia","JP","Japan",
    "5.8.0.0/19","EU","Europe","RU","Russia",
    "223.255.254.0/24","AS","Asia","SG","Singapore",
    "46.36.200.51/32","OC","Oceania","CK","Cook Islands",
    "2.20.183.0/24","EU","Europe","GB","United Kingdom",
];
let IPs = datatable(ip:string)
[
    '2.20.183.12',   // United Kingdom
    '5.8.1.2',       // Russia
    '192.165.12.17', // Unknown
];
IPs
| evaluate ipv4_lookup(IP_Data, ip, network, return_unmatched = true)
```

|ip|network|continent_code|continent_name|country_iso_code|country_name|
|---|---|---|---|---|---|
|2.20.183.12|2.20.183.0/24|EU|歐洲|GB|United Kingdom|
|5.8.1.2|5.8.0.0/19|EU|歐洲|RU|俄羅斯|
|192.165.12.17||||||

### <a name="ipv4-lookup---using-source-in-external_data"></a>IPv4 查閱-使用 external_data 中的來源 ( # A1

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let IP_Data = external_data(network:string,geoname_id:long,continent_code:string,continent_name:string ,country_iso_code:string,country_name:string,is_anonymous_proxy:bool,is_satellite_provider:bool)
    ['https://raw.githubusercontent.com/datasets/geoip2-ipv4/master/data/geoip2-ipv4.csv'];
let IPs = datatable(ip:string)
[
    '2.20.183.12',   // United Kingdom
    '5.8.1.2',       // Russia
    '192.165.12.17', // Sweden
];
IPs
| evaluate ipv4_lookup(IP_Data, ip, network, return_unmatched = true)
```

|ip|network|geoname_id|continent_code|continent_name|country_iso_code|country_name|is_anonymous_proxy|is_satellite_provider|
|---|---|---|---|---|---|---|---|---|
|2.20.183.12|2.20.183.0/24|2635167|EU|歐洲|GB|United Kingdom|0|0|
|5.8.1.2|5.8.0.0/19|2017370|EU|歐洲|RU|俄羅斯|0|0|
|192.165.12.17|192.165.8.0/21|2661886|EU|歐洲|SE|瑞典|0|0|
