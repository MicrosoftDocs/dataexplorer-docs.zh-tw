---
title: 資料列層級安全性（預覽）-Azure 資料總管
description: 本文說明 Azure 資料總管中的資料列層級安全性（預覽）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2020
ms.openlocfilehash: 9952a7a7d95f03ee431b699a1833aa23b21d341b
ms.sourcegitcommit: 4507466bdcc7dd07e6e2a68c0707b6226adc25af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87106357"
---
# <a name="row-level-security-preview"></a>資料列層級安全性（預覽）

使用群組成員資格或執行內容來控制對資料庫資料表中之資料列的存取。

資料列層級安全性（RLS）可簡化安全性的設計和編碼。 它可讓您對應用程式中的資料列存取套用限制。 例如，限制使用者存取其部門的相關資料列，或限制客戶只能存取其公司相關的資料。

存取限制邏輯位於資料庫層，而不是遠離另一個應用層中的資料。 資料庫系統會在每次嘗試從任何層存取資料時套用存取限制。 這個邏輯藉由減少安全性系統的介面區，讓您的安全性系統更可靠且更穩固。

RLS 可讓您將其他應用程式和使用者的存取權提供給資料表的特定部分。 例如，您可能要：

* 僅授與符合某些準則之資料列的存取權
* 匿名部分資料行中的資料
* 以上皆是

如需詳細資訊，請參閱[管理資料列層級安全性原則的控制命令](../management/row-level-security-policy.md)。

> [!NOTE]
> 您在生產資料庫上設定的 RLS 原則也會在執行的資料庫中生效。 您無法在生產和執行中的資料庫上設定不同的 RLS 原則。

> [!TIP]
> 這些函數通常適用于 row_level_security 查詢：
> * [current_principal()](../query/current-principalfunction.md)
> * [current_principal_details()](../query/current-principal-detailsfunction.md)
> * [current_principal_is_member_of()](../query/current-principal-ismemberoffunction.md)

## <a name="limitations"></a>限制

可以設定資料列層級安全性原則的資料表數目沒有限制。

無法在資料表上啟用 RLS 原則：
* 已設定[連續資料匯出](../management/data-export/continuous-data-export.md)的。
* 由[更新原則](./updatepolicy.md)的查詢所參考。
* 已設定的[限制視圖存取原則](./restrictedviewaccesspolicy.md)。

## <a name="examples"></a>範例

### <a name="limit-access-to-sales-table"></a>限制對銷售資料表的存取

在名為的資料表中 `Sales` ，每個資料列都包含有關銷售的詳細資訊。 其中一個資料行包含銷售人員的名稱。 若不讓您的銷售人員存取中的所有記錄 `Sales` ，請在此資料表上啟用資料列層級安全性原則，只傳回銷售人員為目前使用者的記錄：

```kusto
Sales | where SalesPersonAadUser == current_principal()
```

您也可以遮蔽信用卡號碼：

```kusto
Sales | where SalesPersonAadUser == current_principal() | extend CreditCardNumber = "****"
```

如果您想要讓每位銷售人員查看特定國家/地區的所有銷售，您可以定義類似下列的查詢：

```kusto
let UserToCountryMapping = datatable(User:string, Country:string)
[
  "john@domain.com", "USA",
  "anna@domain.com", "France"
];
Sales
| where Country in (UserToCountryMapping | where User == current_principal_details()["UserPrincipalName"] | project Country)
```

如果您有包含管理員的群組，您可能會想要提供所有資料列的存取權給他們。 查詢資料列層級安全性原則。

```kusto
let IsManager = current_principal_is_member_of('aadgroup=sales_managers@domain.com');
let AllData = Sales | where IsManager;
let PartialData = Sales | where not(IsManager) and (SalesPersonAadUser == current_principal());
union AllData, PartialData
| extend CreditCardNumber = "****"
```

### <a name="expose-different-data-to-members-of-different-azure-ad-groups"></a>將不同的資料公開給不同 Azure AD 群組的成員

如果您有多個 Azure AD 群組，而且您想要每個群組的成員查看不同的資料子集，請使用這個適用于 RLS 查詢的結構。 假設使用者只能屬於單一 Azure AD 群組。

```kusto
let IsInGroup1 = current_principal_is_member_of('aadgroup=group1@domain.com');
let IsInGroup2 = current_principal_is_member_of('aadgroup=group2@domain.com');
let IsInGroup3 = current_principal_is_member_of('aadgroup=group3@domain.com');
let DataForGroup1 = Customers | where IsInGroup1 and <filtering specific for group1>;
let DataForGroup2 = Customers | where IsInGroup2 and <filtering specific for group2>;
let DataForGroup3 = Customers | where IsInGroup3 and <filtering specific for group3>;
union DataForGroup1, DataForGroup2, DataForGroup3
```

### <a name="apply-the-same-rls-function-on-multiple-tables"></a>將相同的 RLS 函數套用到多個資料表

首先，定義接收資料表名稱做為字串參數的函式，並使用運算子來參考資料表 `table()` 。 

例如:

```
.create-or-alter function RLSForCustomersTables(TableName: string) {
    table(TableName)
    | ...
}
```

然後以這種方式在多個資料表上設定 RLS：


```
.alter table Customers1 policy row_level_security enable "RLSForCustomersTables('Customers1')"
.alter table Customers2 policy row_level_security enable "RLSForCustomersTables('Customers2')"
.alter table Customers3 policy row_level_security enable "RLSForCustomersTables('Customers3')"
```

### <a name="produce-an-error-upon-unauthorized-access"></a>在未經授權的存取時產生錯誤

如果您想要非授權的資料表使用者收到錯誤，而不是傳回空的資料表，請使用 `[assert()](../query/assert-function.md)` 函數。 下列範例顯示如何在 RLS 函式中產生此錯誤：

```
.create-or-alter function RLSForCustomersTables() {
    MyTable
    | where assert(current_principal_is_member_of('aadgroup=mygroup@mycompany.com') == true, "You don't have access")
}
```

您可以將此方法與其他範例結合。 例如，您可以對不同 AAD 群組中的使用者顯示不同的結果，並為其他人產生錯誤。

## <a name="more-use-cases"></a>更多使用案例

* 「撥打電話中心」支援人員可以透過數個數字的社會安全號碼或信用卡號碼來識別呼叫者。 這些號碼不應完全公開給支援人員。 RLS 原則可以套用在資料表上，以遮罩任何查詢結果集中任何社交安全性或信用卡號碼的最後四個數字。
* 設定 RLS 原則來遮罩個人識別資訊（PII），並讓開發人員查詢生產環境以進行疑難排解，而不違反合規性法規。
* 醫院可以設定 RLS 原則，讓護士只能針對其病人查看資料列。
* 銀行可以根據員工的營業單位或角色，設定 RLS 原則來限制財務資料列的存取權。
* 多租使用者應用程式可以在單一 tableset （有效率）中儲存來自多個租使用者的資料。 他們會使用 RLS 原則，對每個租使用者的資料列強制執行邏輯區隔，讓每個租使用者只能看到它的資料列。

## <a name="performance-impact-on-queries"></a>對查詢的效能影響

在資料表上啟用 RLS 原則時，對存取該資料表的查詢會有一些效能影響。 資料表的存取權實際上會由該資料表上定義的 RLS 查詢所取代。 RLS 查詢的效能影響通常會包含兩個部分：

* Azure Active Directory 中的成員資格檢查
* 套用於資料的篩選

例如:

```kusto
let IsRestrictedUser = current_principal_is_member_of('aadgroup=some_group@domain.com');
let AllData = MyTable | where not(IsRestrictedUser);
let PartialData = MyTable | where IsRestrictedUser and (...);
union AllData, PartialData
```

如果使用者不是的一部分 *some_group@domain.com* ，則 `IsRestrictedUser` 會評估為 `false` 。 將評估的查詢如下所示：

```kusto
let AllData = MyTable;           // the condition evaluates to `true`, so the filter is dropped
let PartialData = <empty table>; // the condition evaluates to `false`, so the whole expression is replaced with an empty table
union AllData, PartialData       // this will just return AllData, as PartialData is empty
```

同樣地，如果 `IsRestrictedUser` 評估為 `true` ，則只會評估的查詢 `PartialData` 。

### <a name="improve-query-performance-when-rls-is-used"></a>使用 RLS 時改善查詢效能

* 如果篩選準則套用在高基數資料行（例如 DeviceID）上，請考慮使用資料[分割原則](./partitioningpolicy.md)或資料[列順序原則](./roworderpolicy.md)
* 如果篩選準則套用於低-中基數資料行，請考慮使用資料列[順序原則](./roworderpolicy.md)

## <a name="performance-impact-on-ingestion"></a>對內嵌的效能影響

對內嵌不會有任何效能影響。
