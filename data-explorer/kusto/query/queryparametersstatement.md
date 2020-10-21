---
title: 查詢參數聲明語句-Azure 資料總管
description: 本文說明 Azure 資料總管中的查詢參數聲明語句。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d9c5e3b41eef933477981945b22b3aceef958dd8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251650"
---
# <a name="query-parameters-declaration-statement"></a>查詢參數聲明語句

::: zone pivot="azuredataexplorer"

傳送至 Kusto 的查詢可能包含一組名稱或值組。 這兩組稱為 *查詢參數*，以及查詢文字本身。 查詢可能會在 *查詢參數宣告語句*中指定名稱和類型，以參考一或多個值。

查詢參數有兩個主要用途：

* 作為針對插入式攻擊的防護機制。
* 做為參數化查詢的方法。

尤其是，在查詢中將使用者提供的輸入結合到 Kusto 的用戶端應用程式，應該使用此機制來防止 Kusto 對等的 [SQL 插入](https://en.wikipedia.org/wiki/SQL_injection) 式攻擊。

## <a name="declaring-query-parameters"></a>宣告查詢參數

若要參考查詢參數、查詢文字或其使用的函式，必須先宣告所使用的查詢參數。 針對每個參數，宣告會提供名稱和純量類型。 （選擇性）參數也可以有預設值。 如果要求未提供參數的具體值，就會使用預設值。 然後，Kusto 會根據該類型的一般剖析規則，來剖析查詢參數的值。

## <a name="syntax"></a>語法

`declare``query_parameters` `(` *Name1* `:` *Type1* [ `=` *DefaultValue1*] [ `,` ...]`);`

* *Name1*：查詢中使用的查詢參數名稱。
* *Type1*：對應的類型，例如 `string` 或 `datetime` 。
  使用者提供的值會編碼為字串，而 Kusto 會將適當的 parse 方法套用至查詢參數，以取得強型別值。
* *DefaultValue1*：參數的選擇性預設值。 這個值必須是適當純量類型的常值。

> [!NOTE]
> 如同 [使用者定義函數](functions/user-defined-functions.md)，類型的查詢參數 `dynamic` 不能有預設值。

## <a name="examples"></a>範例

```kusto
declare query_parameters(UserName:string, Password:string);
print n=UserName, p=hash(Password)
```

```kusto
declare query_parameters(percentage:long = 90);
T | where Likelihood > percentage
```

## <a name="specifying-query-parameters-in-a-client-application"></a>在用戶端應用程式中指定查詢參數

查詢參數的名稱和值會 `string` 由進行查詢的應用程式以值的形式提供。 沒有名稱可重複。

值的解釋是根據查詢參數宣告語句來完成。 每個值都會剖析為查詢主體中的常值。 剖析是根據查詢參數宣告語句所指定的型別來完成。

### <a name="rest-api"></a>REST API

查詢參數是由用戶端應用程式透過 `properties` 要求主體 JSON 物件的位置，在名為的嵌套屬性包中提供 `Parameters` 。 例如，以下是 Kusto 呼叫的 REST API 主體，其會計算某些使用者的年齡，而這是因為應用程式要求使用者的生日。

``` json
{
    "ns": null,
    "db": "myDB",
    "csl": "declare query_parameters(birthday:datetime); print strcat(\"Your age is: \", tostring(now() - birthday))",
    "properties": "{\"Options\":{},\"Parameters\":{\"birthday\":\"datetime(1970-05-11)\",\"courses\":\"dynamic(['Java', 'C++'])\"}}"
}
```

### <a name="kusto-net-sdk"></a>Kusto .NET SDK

若要在使用 Kusto .NET 用戶端程式庫時提供查詢參數的名稱和值，請建立物件的新實例， `ClientRequestProperties` 然後使用 `HasParameter` 、 `SetParameter` 和 `ClearParameter` 方法來操作查詢參數。 這個類別提供許多的強型別多載 `SetParameter` ，其會在內部產生適當的查詢語言常值，並透過 REST API （如上所述）將它傳送為 `string` 。 查詢文字本身仍然必須宣告 [查詢參數](#declaring-query-parameters)。

### <a name="kustoexplorer"></a>Kusto.Explorer

若要設定向服務提出要求時傳送的查詢參數，請使用**查詢參數**"扳手" 圖示 (`ALT`  +  `P`) 。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
