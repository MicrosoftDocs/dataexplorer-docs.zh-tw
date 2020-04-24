---
title: 行級安全性(預覽) - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的行級安全性(預覽)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2020
ms.openlocfilehash: 548b0e9e40cfd709b40e548fe7e0a8ad42aa4abc
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520276"
---
# <a name="row-level-security-preview"></a>列級安全性 (預覽版)

使用組成員身份或執行上下文控制對資料庫表中行的訪問。

行級安全性 (RLS) 允許您對數據列存取應用限制,從而簡化了應用程式中安全性的設計和編碼。 例如,限制使用者訪問與其部門相關的行,或僅限制客戶訪問與其公司相關的數據。

訪問限制邏輯位於資料庫層中,而不是遠離另一個應用程式層中的數據。 每次嘗試從任何層訪問數據時,資料庫系統都會應用訪問限制。 這可藉由縮小安全性系統的接觸區，讓安全性系統更加可靠和健全。

RLS 允許您僅對表的特定部分提供對其他應用程式和/或用戶的訪問。 例如，您可能要：

* 只授予對滿足某些條件的行的存取權限
* 匿名化某些欄中的資料
* 以上兩者

> [!NOTE]
> 無法在表上啟用行層級安全原則:
> * 匯出為其設定[連續資料匯出](../management/data-export/continuous-data-export.md)。
> * 這是由某些[更新策略](./updatepolicy.md)的查詢引用的。
> * 在哪個上設定[了受限檢視存取原則](./restrictedviewaccesspolicy.md)。

有關管理列級安全原則的控制命令,[請參考此處](../management/row-level-security-policy.md)。

## <a name="examples"></a>範例

在名為的`Sales`表中,每行包含有關銷售的詳細資訊。 其中一列包含銷售人員的姓名。
您可以啟用此表上的行等級安全原則,僅`Sales`傳回銷售人員是目前使用者的記錄,而不是授予銷售人員對中的所有記錄的存取權限:

```kusto
Sales | where SalesPersonAadUser == current_principal()
```

您還可以遮罩信用卡號:

```kusto
Sales | where SalesPersonAadUser == current_principal() | extend CreditCardNumber = "****"
```

如果希望每個銷售人員查看特定國家/地區的所有銷售,可以定義類似於以下內容的查詢:

```kusto
let UserToCountryMapping = datatable(User:string, Country:string)
[
  "john@domain.com", "USA",
  "anna@domain.com", "France"
];
Sales
| where Country in (UserToCountryMapping | where User == current_principal_details()["UserPrincipalName"] | project Country)
```

如果您有一個包含銷售人員經理的 AAD 組,您可能希望他們有權訪問所有行。 這可以通過行級別安全原則中的以下查詢來實現:

```kusto
let IsManager = current_principal_is_member_of('aadgroup=sales_managers@domain.com');
let AllData = Sales | where IsManager;
let PartialData = Sales | where not(IsManager) and (SalesPersonAadUser == current_principal());
union AllData, PartialData
| extend CreditCardNumber = "****"
```

通常,如果您有多個 AAD 組,並且希望每個組的成員看到不同的資料子集,則可以遵循 RLS 查詢的此結構(假設使用者只能屬於單個 AAD 組):

```kusto
let IsInGroup1 = current_principal_is_member_of('aadgroup=group1@domain.com');
let IsInGroup2 = current_principal_is_member_of('aadgroup=group2@domain.com');
let IsInGroup3 = current_principal_is_member_of('aadgroup=group3@domain.com');
let DataForGroup1 = Customers | where IsInGroup1 and <filtering specific for group1>;
let DataForGroup2 = Customers | where IsInGroup2 and <filtering specific for group2>;
let DataForGroup3 = Customers | where IsInGroup3 and <filtering specific for group3>;
union DataForGroup1, DataForGroup2, DataForGroup3
```

## <a name="more-use-cases"></a>更多用例

* 呼叫中心支持人員可以通過其社會保險號或信用卡號碼的幾位數字來識別呼叫者。 這些數位不應完全暴露給支助人員。 可以在表上應用 RLS 策略,以遮罩任何查詢的結果集中任何社會保險或信用卡號的最後四位數位。
* 設置遮罩個人身份資訊 (PII) 的 RLS 策略,使開發人員能夠在不違反法規的情況下查詢生產環境以進行故障排除。
* 醫院可以設置 RLS 策略,允許護士僅查看患者的數據行。
* 銀行可以設置 RLS 策略,以根據員工的營業單位或角色限制對財務數據行的訪問。
* 多租戶應用程式可以將來自多個租戶的數據存儲在單個表集中(非常高效)。 他們將使用 RLS 策略強制將每個租戶的數據行與所有其他租戶的行進行邏輯分離,以便每個租戶只能查看其數據行。

## <a name="performance-impact-on-queries"></a>對查詢效能影響

在表上啟用 RLS 策略時,將對訪問該表的查詢產生一些性能影響。 對表的訪問實際上將被該表上定義的 RLS 查詢替換。 RLS 查詢的效能影響通常由兩部分組成:

* Azure 活動目錄中的成員身份檢查
* 套用資料的篩選器

例如：

```kusto
let IsRestrictedUser = current_principal_is_member_of('aadgroup=some_group@domain.com');
let AllData = MyTable | where not(IsRestrictedUser);
let PartialData = MyTable | where IsRestrictedUser and (...);
union AllData, PartialData
```

如果使用者不是的一部分some_group@domain.com,`IsRestrictedUser`則將計算`false`到 ,因此要計算的查詢將類似於此查詢:

```kusto
let AllData = MyTable;           // the condition evaluates to `true`, so the filter is dropped
let PartialData = <empty table>; // the condition evaluates to `false`, so the whole expression is replaced with an empty table
union AllData, PartialData       // this will just return AllData, as PartialData is empty
```

同樣,如果`IsRestrictedUser`計算到`true`,則將僅`PartialData`計算 的查詢。

## <a name="performance-impact-on-ingestion"></a>對攝入的性能影響

無。