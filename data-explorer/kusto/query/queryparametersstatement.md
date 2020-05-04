---
title: 查詢參數宣告語句-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的查詢參數宣告聲明。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: a76755d04179b3d311e79798162c61db764455d7
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737805"
---
# <a name="query-parameters-declaration-statement"></a>查詢參數宣告語句

::: zone pivot="azuredataexplorer"

除了查詢文字本身以外，傳送至 Kusto 的查詢可能會包含一組稱為**查詢參數**的名稱/值配對。 然後，查詢文字可以透過**查詢參數宣告語句**來指定名稱和類型，藉以參考一或多個查詢參數值。

查詢參數有兩個主要用途：

1. 作為防止**插入式攻擊**的保護機制。
2. 作為參數化查詢的方式。

特別是，在查詢中將使用者提供的輸入結合到 Kusto 的用戶端應用程式，應該使用這項機制來防止 Kusto 對等的[SQL 插入](https://en.wikipedia.org/wiki/SQL_injection)式攻擊。

## <a name="declaring-query-parameters"></a>宣告查詢參數

若要能夠參考查詢參數，查詢文字（或它使用的函式）必須先宣告所使用的查詢參數。 針對每個參數，宣告會提供名稱和純量類型。 （選擇性）如果要求未提供參數的具體值，則參數也可以有預設值來使用。 然後，Kusto 會根據該類型的一般剖析規則來分析查詢參數的值。

**語法**

`declare``query_parameters` `:` *Type1* `,` *Name1* `=` Name1 Type1 [ *DefaultValue1*] [ `(` ...]`);`

* *Name1*：查詢中使用的查詢參數名稱。
* *Type1*：對應的類型（例如`string`、 `datetime`等）使用者所提供的值會編碼為字串，而 Kusto 會將適當的 parse 方法套用至查詢參數，以取得強型別值。
* *DefaultValue1*：參數的選擇性預設值。 這必須是適當純量類型的常值。

> [!NOTE]
> 如同[使用者定義函數](functions/user-defined-functions.md)，類型`dynamic`的查詢參數不能有預設值。

**範例**

```kusto
declare query_parameters(UserName:string, Password:string);
print n=UserName, p=hash(Password)
```

```kusto
declare query_parameters(percentage:long = 90);
T | where Likelihood > percentage
```

## <a name="specifying-query-parameters-in-a-client-application"></a>在用戶端應用程式中指定查詢參數

查詢參數的名稱和值會由進行查詢`string`的應用程式以值的形式提供。 不能重複任何名稱。

值的解讀是根據查詢參數宣告語句來完成。 每個值都會根據查詢參數宣告語句所指定的類型，剖析為查詢主體中的常值。

### <a name="rest-api"></a>REST API

查詢參數是由用戶端應用程式在`properties`名`Parameters`為的嵌套屬性包中，透過要求主體的 JSON 物件的位置提供。 例如，以下是 REST API 呼叫 Kusto 的主體，其會計算某些使用者的年齡（假設應用程式會要求使用者輸入其生日）：

``` json
{
    "ns": null,
    "db": "myDB",
    "csl": "declare query_parameters(birthday:datetime); print strcat(\"Your age is: \", tostring(now() - birthday))",
    "properties": "{\"Options\":{},\"Parameters\":{\"birthday\":\"datetime(1970-05-11)\",\"courses\":\"dynamic(['Java', 'C++'])\"}}"
}
```

### <a name="kusto-net-sdk"></a>Kusto .NET SDK

若要在使用 Kusto .net 用戶端程式庫時提供查詢參數的名稱和值，一個會`ClientRequestProperties`建立物件的新實例，然後使用`HasParameter`、 `SetParameter`和`ClearParameter`方法來操作查詢參數。 請注意，這個類別會提供一些的強型別多載`SetParameter`。就內部而言，它們會產生適當的查詢語言常值，並`string`透過 REST API 將它當做傳回，如上所述。 查詢文字本身仍然必須宣告[查詢](#declaring-query-parameters)參數。

### <a name="kustoexplorer"></a>Kusto.Explorer

若要設定對服務提出要求時所傳送的查詢參數，請使用**查詢參數**「扳手」圖示（`ALT` + `P`）。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
