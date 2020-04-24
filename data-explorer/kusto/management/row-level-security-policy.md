---
title: 行級安全策略(預覽) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 RowLevel 安全策略(預覽)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2020
ms.openlocfilehash: bf8ee8bc4c43c4ed3bcd320ce5b4778f33c3cc64
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520293"
---
# <a name="rowlevelsecurity-policy-preview"></a>行等級安全原則 (預覽版)

本文介紹了用於配置資料庫表[row_level_security策略](rowlevelsecuritypolicy.md)的命令。

## <a name="displaying-the-policy"></a>顯示策略

要顯示策略,請使用以下指令:

```kusto
.show table <table_name> policy row_level_security
```

## <a name="configuring-the-policy"></a>設定原則

在表上啟用row_level_security政策:

```kusto
.alter table <table_name> policy row_level_security enable "<query>"
```

關閉表上的row_level_security原則:

```kusto
.alter table <table_name> policy row_level_security disable "<query>"
```

即使禁用策略,也可以強制它應用於單個查詢。 在查詢之前輸入以下行:

`set query_force_row_level_security;`

如果要嘗試各種查詢row_level_security,但不希望它實際對使用者生效,這非常有用。 對查詢有信心後,請啟用策略。

> [!NOTE]
> 以下限制適用於`query`:
>
> * 查詢應生成與定義策略的表完全相同的架構。 也就是說,查詢的結果應返回與原始表完全相同的列,順序相同,具有相同的名稱和類型。
> * 查詢只能使用以下運算`extend`子 : `where`、 `project` `project-away` `project-rename`、 `project-reorder` `union`、 、 、 與與 。
> * 查詢不能引用定義策略的表以外的表。
> * 查詢可以是以下任一查詢,也可以是它們的組合:
>    * 查詢(例如), `<table_name> | extend CreditCardNumber = "****"`
>    * 函數(例如) `AnonymizeSensitiveData`
>    * 資料表(例如) `datatable(Col1:datetime, Col2:string) [...]`

> [!TIP]
> 這些函數通常可用於row_level_security查詢:
> * [current_principal()](../query/current-principalfunction.md)
> * [current_principal_details()](../query/current-principal-detailsfunction.md)
> * [current_principal_is_member_of()](../query/current-principal-ismemberoffunction.md)

**範例**

```kusto
.create-or-alter function with () TrimCreditCardNumbers() {
    let UserCanSeeFullNumbers = current_principal_is_member_of('aadgroup=super_group@domain.com');
    let AllData = Customers | where UserCanSeeFullNumbers;
    let PartialData = Customers | where not(UserCanSeeFullNumbers) | extend CreditCardNumber = "****";
    union AllData, PartialData
}

.alter table Customers policy row_level_security enable "TrimCreditCardNumbers"
```
**性能說明**`UserCanSeeFullNumbers`: 將首先計算,然後`AllData``PartialData`計算或將評估,但不能同時計算兩者,這是預期的結果。
您可以在此處閱讀更多有關 RLS 對效能的影響[。](rowlevelsecuritypolicy.md#performance-impact-on-queries)

## <a name="deleting-the-policy"></a>移除原則

```kusto
.delete table <table_name> policy row_level_security
```