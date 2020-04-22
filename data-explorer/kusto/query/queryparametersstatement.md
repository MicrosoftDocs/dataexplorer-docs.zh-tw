---
title: 查詢參數宣告語句 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的查詢參數聲明語句。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b7193ada6967882306d9a977b6c90af8b247036d
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765490"
---
# <a name="query-parameters-declaration-statement"></a>查詢參數宣告敘述

::: zone pivot="azuredataexplorer"

發送到 Kusto 的查詢除了查詢文本本身外,還包括一組稱為**查詢參數**的名稱/值對。 然後,查詢文本可以通過**查詢參數聲明語句**指定一個或多個查詢參數值並鍵入來引用它們的名稱和類型。

查詢參數有兩個主要用途:

1. 作為防止**注射攻擊**的保護機制。
2. 作為對查詢進行參數化的一種方式。

特別是,將使用者提供的輸入合併到它們然後發送到 Kusto 的用戶端應用程式應該使用此機制來防止[Sql 注入](https://en.wikipedia.org/wiki/SQL_injection)攻擊的 Kusto 等效攻擊。

## <a name="declaring-query-parameters"></a>宣告查詢參數

為了能夠引用查詢參數,查詢文本(或其使用的函數)必須首先聲明它使用的查詢參數。 對於每個參數,聲明提供名稱和標量類型。 或者,如果請求未為參數提供具體值,則參數還可以具有要使用的預設值。 然後,Kusto 會根據查詢參數的法常分析規則分析查詢參數的值。

**語法**

`declare``query_parameters``(`名稱1 型態 1 = 預設值1 [ ] ...] `:` * * `,` `=` * * * *`);`

* *名稱1*:查詢中使用的查詢參數的名稱。
* *類型 1:* 相應的類型`string``datetime`(例如 , 等)使用者提供的值被編碼為字串,Kusto 將應用相應的解析方法到查詢參數以獲取強類型值。
* *預設值1*:參數的可選預設值。 這必須是相應標量類型的字面。

> [!NOTE]
> 與[使用者定義的函數](functions/user-defined-functions.md)一樣,類型`dynamic`的 查詢參數不能具有預設值。

**範例**

```kusto
declare query_parameters(UserName:string, Password:string);
print n=UserName, p=hash(Password)
```

```kusto
declare query_parameters(percentage:long = 90);
T | where Likelihood > percentage
```

## <a name="specifying-query-parameters-in-a-client-application"></a>在客戶端應用程式中指定查詢參數

查詢參數的名稱和值由進行查詢的應用程式作為`string`值提供。 不能重複任何名稱。

根據查詢參數聲明語句對值的解釋。 根據查詢參數聲明語句指定的類型,每個值都像是查詢正文中的文本一樣解析。

### <a name="rest-api"></a>REST API

查詢參數由客戶端應用程式通過請求正文`properties`的 JSON 物件的插`Parameters`槽在稱為的嵌套屬性包中提供。 例如,下面是對 Kusto 的 REST API 呼叫的正文,該調用計算了某個使用者的年齡(大概是透過讓應用程式詢問使用者的生日):

``` json
{
    "ns": null,
    "db": "myDB",
    "csl": "declare query_parameters(birthday:datetime); print strcat(\"Your age is: \", tostring(now() - birthday))",
    "properties": "{\"Options\":{},\"Parameters\":{\"birthday\":\"datetime(1970-05-11)\",\"courses\":\"dynamic(['Java', 'C++'])\"}}"
}
```

### <a name="kusto-net-sdk"></a>函式庫托 .NET SDK

在使用 Kusto .NET 用戶端庫時,為了提供查詢參數的名稱`ClientRequestProperties`和值, 創建物件的新實`HasParameter`例`SetParameter`,然後`ClearParameter`使用、 和方法操作查詢參數。 請注意,此類為`SetParameter`提供了許多強類型的重載。在內部,它們生成查詢語言的適當文本,並通過 REST`string`API 將其作為發送形式發送,如上所述。 查詢文字本身必須[宣告查詢參數](#declaring-query-parameters)。

### <a name="kustoexplorer"></a>Kusto.Explorer

要設置向服務發出請求時發送的查詢參數,請使用**查詢參數**「扳手」圖示`ALT` + `P`()。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援此功能

::: zone-end
