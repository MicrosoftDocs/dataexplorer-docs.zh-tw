---
title: Azure 資料總管儀表板中的參數
description: 使用參數做為儀表板篩選器的建立區塊。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 4921a48ff879879084ec1941ab69c6e9d29b9773
ms.sourcegitcommit: a4779e31a52d058b07b472870ecd2b8b8ae16e95
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89366158"
---
# <a name="use-parameters-in-azure-data-explorer-dashboards"></a>在 Azure 資料總管儀表板中使用參數

參數會用來作為 Azure 中儀表板篩選器的建立區塊資料總管儀表板。 它們是在儀表板範圍中進行管理，而且可以新增至查詢來篩選基礎視覺效果所呈現的資料。 查詢可以使用一或多個參數。 本檔說明如何在 Azure 資料總管儀表板中建立及使用參數和連結篩選。 

> [!NOTE]
> 參數管理可在編輯模式中提供給儀表板編輯器。

## <a name="prerequisites"></a>先決條件

[使用 Azure 資料總管儀表板將資料視覺化](azure-data-explorer-dashboards.md)

## <a name="view-parameters-list"></a>視圖參數清單

選取儀表板頂端的 [ **參數** ] 按鈕，以查看所有儀表板參數的清單。

:::image type="content" source="media/dashboard-parameters/dashboard-icons.png" alt-text="儀表板頂端的參數按鈕":::

## <a name="create-a-parameter"></a>建立參數

若要建立參數，請選取右窗格頂端的 [ **新增參數** ] 按鈕。

:::image type="content" source="media/dashboard-parameters/new-parameter-button.png" alt-text="新參數按鈕":::

### <a name="properties"></a>屬性

1. 在 [ **新增參數** ] 窗格中，設定以下詳細說明的屬性。

:::image type="content" source="media/dashboard-parameters/properties.png" alt-text="新增參數屬性":::

|欄位  |描述 |
|---------|---------|
|**參數顯示名稱**    |   儀表板或編輯卡片上顯示的參數名稱。      |
|**參數類型**    |下列其中一個參數：<ul><li>**單一選取**：可在篩選中選取一個值，作為參數的輸入。</li><li>**多重選取**：可以在篩選中選取一個或多個值，作為參數的輸入 (s) 。</li><li>**時間範圍**：允許建立額外的參數，以根據時間篩選查詢和儀表板。 依預設，每個儀表板都有時間範圍選擇器。</li><li>**自由文字**：篩選中未填入任何值。 使用者可以輸入值，或複製/貼上文字欄位的值。 篩選會保留最近使用的值。</li></ul>    |
|**變數名稱**     |   要在查詢中使用的參數名稱。      |
|**Data type**    |    參數值的資料類型。     |
|**釘選為儀表板篩選**   |   將參數型篩選釘選到儀表板或從儀表板取消釘選。       |
|**Source**     |    參數值的來源： <ul><li>**固定值**：手動引進靜態篩選值。 </li><li>**查詢**：使用 KQL 查詢動態引進值。  </li></ul>    |
|**新增「全選」值**    |   僅適用于單一選取專案和多重選取參數類型。 用來取得所有參數值的資料。 此值應該內建在查詢中以提供功能。 如需建立這類查詢的更多範例，請參閱 [使用多重選取查詢型參數](#use-the-multiple-selection-query-based-parameter) 。     |

## <a name="manage-parameters-in-parameter-card"></a>管理參數卡中的參數

在參數卡的三個點功能表中，選取 [**編輯**]、[**重複參數**]、[**刪除**] 或 [**從儀表板** / **釘選到儀表板**]。

您可以在參數卡中查看下列指示器：
* 參數顯示名稱 
* 變數名稱 
* 使用參數的查詢數目
* 在儀表板中釘選/取消釘選狀態

:::image type="content" source="media/dashboard-parameters/modify-parameter.png" alt-text="修改參數":::

## <a name="use-parameters-in-your-query"></a>在查詢中使用參數

您必須在查詢中使用參數，才能讓篩選準則適用于該查詢視覺效果。 一旦定義之後，您就可以在 [ **查詢** ] 頁面中看到參數，> 篩選 top bar 和查詢 intellisense 中。

:::image type="content" source="media/dashboard-parameters/query-intellisense.png" alt-text="查看頂端列和 intellisense 中的參數":::

> [!NOTE]
> 如果查詢中未使用參數，則篩選會維持停用狀態。 一旦將參數加入至查詢之後，篩選準則就會變成作用中狀態。

## <a name="use-different-parameter-types"></a>使用不同的參數類型

支援數個儀表板參數類型。 下列範例說明如何在查詢中使用各種參數類型的參數。 

### <a name="use-the-default-time-range-parameter"></a>使用預設時間範圍參數

每個儀表板預設都有 *時間範圍* 參數。 只有在查詢中使用時，它才會顯示在儀表板上做為篩選器。 使用參數關鍵字 `_startTime` ，並 `__endTime` 在查詢中使用預設時間範圍參數，如下列範例所示：

```kusto
EventsAll
| where Repo.name has 'Microsoft'
| where CreatedAt between (_startTime.._endTime)
| summarize TotalEvents = count() by RepoName=tostring(Repo.name)
| top 5 by TotalEvents
```

儲存之後，時間範圍篩選器會顯示在儀表板上。 現在可以用來篩選卡片上的資料。 您可以從下拉式清單中選取 [ **時間範圍** (過去 x 分鐘/小時/天]) 或 **自訂的時間範圍**，以篩選您的儀表板。

:::image type="content" source="media/dashboard-parameters/time-range.png" alt-text="使用自訂時間範圍進行篩選":::

### <a name="use-the-single-selection-fixed-value-parameter"></a>使用單一選取固定值參數

固定值參數是以使用者指定的預先定義值為基礎。 下列範例說明如何建立單一選取固定值參數。

#### <a name="create-the-parameter"></a>建立參數

1. 選取 [ **參數** ] 以開啟 [ **參數** ] 窗格，然後選取 [ **新增參數**]。

1. 填入詳細資料，如下所示：

    * **參數顯示名稱**：公司
    * **參數類型**：單一選取
    * **變數名稱**： `_company`
    * **資料類型**：字串
    * **釘選為儀表板篩選**：已核取
    * **來源**：固定值
    
    在此範例中，請使用下列值：
    
    | 值      | 參數顯示名稱 |
    |------------|------------------------|
    | google    | Google                 |
    | 微軟 | Microsoft              |
    | facebook  | Facebook               |
    | aws       | AWS                    |
    | uber      | Uber                   |
    
    * 新增 **全選** 的值：未選取
    * 預設值： Microsoft

1. 選取 [ **完成** ] 以建立參數。

這些參數可以出現在 [ **參數** ] 側邊窗格中，但目前並未用於任何視覺效果中。

:::image type="content" source="media/dashboard-parameters/start-end-side-pane.png" alt-text="側邊窗格中的開始時間結束時間參數":::

#### <a name="use-the-parameter"></a>使用參數

1. 使用新的 *公司* 參數執行範例查詢，方法是使用 `_company` 變數名稱：

    ```kusto
    EventsAll
    | where CreatedAt > ago(7d)
    | where Type == "WatchEvent"
    | where Repo.name has _company
    | summarize WatchEvents=count() by RepoName = tolower(tostring(Repo.name))
    | top 5 by WatchEvents
    ```

    新的參數會顯示在儀表板頂端的參數清單中。

1. 選取不同的值來更新視覺效果。

    :::image type="content" source="media/dashboard-parameters/top-five-repos.png" alt-text="前五個存放庫結果":::

### <a name="use-the-multiple-selection-fixed-value-parameters"></a>使用多重選取固定值參數

固定值參數是以使用者指定的預先定義值為基礎。 下列範例會示範如何建立和使用多重選取的固定值參數。

#### <a name="create-the-parameters"></a>建立參數

1. 選取 [ **參數** ] 以開啟 [ **參數** ] 窗格，然後選取 [ **新增參數**]。

1. 填入 [使用單一選取固定值參數](#use-the-single-selection-fixed-value-parameter) 中所述的詳細資料，並進行下列變更：

    * **參數顯示名稱**：公司
    * **參數類型**：多重選取
    * **變數名稱**： `_companies`

1. 按一下 [ **完成** ] 以建立參數。

新的參數可以出現在 [ **參數** ] 側邊窗格中，但目前並未用於任何視覺效果中。

:::image type="content" source="media/dashboard-parameters/companies-side-pane.png" alt-text="公司側邊窗格":::

#### <a name="use-the-parameters"></a>使用參數 

1. 使用新的 *公司* 參數執行範例查詢，方法是使用 `_companies` 變數。

    ```kusto
    EventsAll
    | where CreatedAt > ago(7d)
    | extend RepoName = tostring(Repo.name)
    | where Type == "WatchEvent"
    | where RepoName has_any (_companies)
    | summarize WatchEvents=count() by RepoName
    | top 5 by WatchEvents
    ```

    新的參數會顯示在儀表板頂端的參數清單中。  

1. 選取一或多個不同的值來更新視覺效果。

    :::image type="content" source="media/dashboard-parameters/select-companies.png" alt-text="選取公司":::

### <a name="use-the-single-selection-query-based-parameter"></a>使用單一選取專案查詢型參數

藉由執行參數查詢，在儀表板載入期間抓取以查詢為基礎的參數值。 下列範例會示範如何建立和使用單一選取專案查詢型參數。

#### <a name="create-the-parameter"></a>建立參數

1. 選取 [ **參數** ] 以開啟 [ **參數** ] 窗格，然後選取 [ **新增參數**]。

2. 填入 [使用單一選取固定值參數](#use-the-single-selection-fixed-value-parameter) 中所述的詳細資料，並進行下列變更：

    * **參數顯示名稱**：事件
    * **變數名稱**： `_event`
    * **來源**：查詢
    * **資料來源**： GitHub
    * 選取 [ **加入查詢** ]，然後輸入下列查詢。 選取 [完成]。

    ```kusto
    EventsAll
    | distinct Type
    | order by Type asc
    ```

    * **值**：類型
    * **顯示名稱**：類型
    * **預設值**： WatchEvent

1. 選取 [ **完成** ] 以建立參數。

#### <a name="use-a-parameter-in-the-query"></a>在查詢中使用參數

1. 下列使用新事件參數的查詢範例會使用 `_ event` 變數：

    ``` kusto
    EventsAll
    | where Type has (_event)
    | summarize count(Id) by Type, bin(CreatedAt,7d)
    ```

    新的參數會顯示在儀表板頂端的參數清單中。 

1. 選取不同的值來更新視覺效果。

### <a name="use-the-multiple-selection-query-based-parameter"></a>使用多重選取查詢型參數

以查詢為基礎的參數值是在儀表板載入時間，藉由執行使用者指定的查詢來衍生。 下列範例會示範如何建立多重選取查詢型參數：

#### <a name="create-a-parameter"></a>建立參數

1. 選取 [ **參數** ] 以開啟 [ **參數** ] 窗格，然後選取 [ **新增參數**]。

1. 填入 [使用單一選取固定值參數](#use-the-single-selection-fixed-value-parameter) 中所述的詳細資料，並進行下列變更：

    * **參數顯示名稱**：事件
    * **參數類型**：多重選取
    * **變數名稱**： `_events`

1. 按一下 [ **完成** ] 以建立參數。

#### <a name="use-parameters-in-the-query"></a>在查詢中使用參數

1. 下列範例查詢會使用新的 *Events* 參數，並使用 `_events` 變數。

    ``` kusto
    EventsAll
    | where Type in (_event) or isempty(_events)
    | summarize count(Id) by Type, bin(CreatedAt,7d)
    ```

    > [!NOTE]
    > 此範例會使用函式來檢查是否有空白值，以使用 [全 **選** ] 選項 `isempty()` 。

    新的參數會顯示在儀表板頂端的參數清單中。 

1. 選取一或多個不同的值來更新視覺效果。

### <a name="use-the-free-text-parameter"></a>使用免費文字參數

自由文字參數不包含任何值。 它們可讓您引進自己的價值。

#### <a name="create-the-parameter"></a>建立參數

1. 選取 [ **參數** ] 以開啟 [ **參數] 窗格** ，然後選取 [ **新增參數**]。
1. 填入詳細資料，如下所示：
    * **參數顯示名稱**：公司
    * **參數類型**：自由文字
    * **變數名稱**： _company
    * **資料類型**：字串
    * **釘選為儀表板篩選**：已核取
    * **預設值**：沒有預設值

#### <a name="use-parameters-in-the-query"></a>在查詢中使用參數

1. 使用新的 *公司* 參數執行範例查詢，方法是使用 `_company` 變數名稱：

    ```kusto
    EventsAll
    | where CreatedAt > ago(7d)
    | where Type == "WatchEvent"
    | where Repo.name has _company
    | summarize WatchEvents=count() by RepoName = tolower(tostring(Repo.name))
    | top 5 by WatchEvents
    ```

新的參數現在會顯示在儀表板頂端的參數清單中。

## <a name="use-filter-search-for-single-and-multiple-selection-filters"></a>使用篩選準則搜尋單一和多重選取範圍篩選

在 [單一] 和 [多重選取] 篩選中，輸入您想要的值。 篩選準則搜尋會顯示符合搜尋詞彙的所有最近抓取值。

## <a name="next-steps"></a>後續步驟

* [自訂儀表板視覺效果](dashboard-customize-visuals.md)
* [在 Azure 資料總管中查詢資料](web-query-data.md) 

