---
title: 查詢參數宣告語句-Azure 資料總管
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
ms.openlocfilehash: 0373525d0f1e369af31b17595900128e0d4e0bf4
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763343"
---
# <a name="query-parameters-declaration-statement"></a>查詢參數宣告語句

::: zone pivot="azuredataexplorer"

傳送至 Kusto 的查詢可能會包含一組名稱或值配對。 配對稱為*查詢參數*，以及查詢文字本身。 查詢可以藉由在*查詢參數宣告語句*中指定名稱和類型，來參考一個或多個值。

查詢參數有兩個主要用途：

* 作為防止插入式攻擊的保護機制。
* 作為參數化查詢的方式。

特別是，在查詢中將使用者提供的輸入結合到 Kusto 的用戶端應用程式，應該使用此機制來防止 Kusto 對等的[SQL 插入](https://en.wikipedia.org/wiki/SQL_injection)式攻擊。

## <a name="declaring-query-parameters"></a>宣告查詢參數

若要參考查詢參數、查詢文字或其所使用的函式，必須先宣告所使用的查詢參數。 針對每個參數，宣告會提供名稱和純量類型。 （選擇性）參數也可以有預設值。 如果要求未提供參數的具體值，則會使用預設的。 然後，Kusto 會根據該類型的一般剖析規則，分析查詢參數的值。

**語法**

`declare``query_parameters` `(` *Name1* `:` *Type1* [ `=` *DefaultValue1*] [ `,` ...]`);`

* *Name1*：查詢中使用的查詢參數名稱。
* *Type1*：對應的類型，例如 `string` 或 `datetime` 。
  使用者所提供的值會編碼為字串，而 Kusto 會將適當的 parse 方法套用至查詢參數，以取得強型別值。
* *DefaultValue1*：參數的選擇性預設值。 這個值必須是適當純量類型的常值。

> [!NOTE]
> 如同[使用者定義函數](functions/user-defined-functions.md)，類型的查詢參數 `dynamic` 不能有預設值。

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

查詢參數的名稱和值會 `string` 由進行查詢的應用程式以值的形式提供。 不能重複任何名稱。

值的解讀是根據查詢參數宣告語句來完成。 每個值都會剖析為查詢主體中的常值。 剖析是根據查詢參數宣告語句所指定的類型來完成。

### <a name="rest-api"></a>REST API

查詢參數是由用戶端應用程式 `properties` 在名為的嵌套屬性包中，透過要求主體的 JSON 物件的位置提供 `Parameters` 。 例如，以下是 REST API 呼叫 Kusto 的主體，它會計算某些使用者的年齡，這是因為應用程式會要求使用者的生日。

``` json
{
    "ns": null,
    "db": "myDB",
    "csl": "declare query_parameters(birthday:datetime); print strcat(\"Your age is: \", tostring(now() - birthday))",
    "properties": "{\"Options\":{},\"Parameters\":{\"birthday\":\"datetime(1970-05-11)\",\"courses\":\"dynamic(['Java', 'C++'])\"}}"
}
```

### <a name="kusto-net-sdk"></a>Kusto .NET SDK

若要在使用 Kusto .NET 用戶端程式庫時提供查詢參數的名稱和值，一個會建立物件的新實例， `ClientRequestProperties` 然後使用 `HasParameter` 、 `SetParameter` 和 `ClearParameter` 方法來操作查詢參數。 這個類別會提供一些的強型別多載 `SetParameter` ; 在內部，它們會產生適當的查詢語言常值，並透過 REST API 將它傳送為 `string` ，如上所述。 查詢文字本身仍然必須宣告[查詢參數](#declaring-query-parameters)。

### <a name="kustoexplorer"></a>Kusto.Explorer

若要設定對服務提出要求時所傳送的查詢參數，請使用**查詢參數**「扳手」圖示（ `ALT`  +  `P` ）。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
