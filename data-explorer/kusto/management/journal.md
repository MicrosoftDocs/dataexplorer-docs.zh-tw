---
title: 日誌管理-Azure 資料總管
description: 本文說明 Azure 資料總管中的日誌管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/19/2019
ms.openlocfilehash: 64b0f8179a328ce811747b05a90516fd8b6029be
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294401"
---
# <a name="journal-management"></a>日誌管理

 `Journal`包含在 Azure 資料總管資料庫上完成的中繼資料作業相關資訊。

中繼資料作業可能是由使用者執行的控制命令，或系統執行的內部控制命令所產生，例如透過保留來放置範圍。

> [!NOTE]
> * 包含*加入*新範圍的中繼資料作業（例如 `.ingest` 、 `.append` `.move` 和其他）將不會有所顯示的相符事件 `Journal` 。
> * 結果集資料行中的資料，以及其呈現的格式不是合約。 
  不建議您對這些專案進行相依性。

|事件        |EventTimestamp     |資料庫  |EntityName|UpdatedEntityName|EntityVersion|Entitycontainername.entitysetname|
|-------------|-------------------|----------|----------|-----------------|-------------|-------------------|
|建立-資料表 |2017-01-05 14:25:07|InternalDb|MyTable1  |MyTable1         |7.0 版         |InternalDb         |
|重新命名資料表 |2017-01-13 10:30:01|InternalDb|MyTable1  |MyTable2         |8.0 版         |InternalDb         |  

|OriginalEntityState|UpdatedEntityState                                              |ChangeCommand                                                                                                          |主體            |
|-------------------|----------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|---------------------|
|.                  |名稱： MyTable1，屬性： Name = ' [MyTable1]。[col1] '，類型 = ' I32 '|。建立資料表 MyTable1 （col1： int）                                                                                      |imike@fabrikam.com
|.                  |資料庫屬性（在此顯示太長）         |。 create database TestDB 會保存（@ " https://imfbkm.blob.core.windows.net/md "，@ " https://imfbkm.blob.core.windows.net/data "）|Azure AD 應用程式識別碼 = 76263cdb-abcd-545644e9c404
|名稱： MyTable1，屬性： Name = ' [MyTable1]。[col1] '，類型 = ' I32 '|名稱： MyTable2，屬性： Name = ' [MyTable1]。[col1] '，類型 = ' I32 '|。將 table MyTable1 重新命名為 MyTable2|rdmik@fabrikam.com

|Item                 |描述                                                              |                                
|---------------------|-------------------------------------------------------------------------|
|事件                |中繼資料事件名稱                                                  |
|EventTimestamp       |事件時間戳記                                                      |                        
|資料庫             |此資料庫的中繼資料已在事件之後變更                |
|EntityName           |作業執行所在的機構名稱，在變更之前    |
|UpdatedEntityName    |變更後的新機構名稱                                     |
|EntityVersion        |變更後的新中繼資料版本（db/cluster）               |
|Entitycontainername.entitysetname  |實體容器名稱（entity = column，container = table）               |
|OriginalEntityState  |實體（實體屬性）在變更之前的狀態            |
|UpdatedEntityState   |變更後的新狀態                                           |
|ChangeCommand        |觸發中繼資料變更的已執行控制命令          |
|主體            |執行控制命令的主體（使用者/應用程式）               |
    
## <a name="show-journal"></a>。顯示日誌

`.show journal`命令會傳回資料庫或使用者具有系統管理員存取權的叢集上的中繼資料變更清單。

**權限**

每個人（叢集存取）都可以執行命令。 

傳回的結果將包含： 
- 執行命令之使用者的所有記錄項目。 
- 執行命令的使用者具有系統管理員存取權的所有資料庫記錄項目。 
- 所有叢集記錄項目（如果執行命令的使用者是叢集系統管理員）。 

## <a name="show-database-databasename-journal"></a>。顯示資料庫*DatabaseName*日誌 

`.show` `database` *DatabaseName* `journal` 命令會針對特定的資料庫中繼資料變更傳回日誌。

**權限**

每個人（叢集存取）都可以執行命令。 傳回的結果包括： 
- 如果執行命令的使用者是*DatabaseName*中的資料庫管理員，則為資料庫*DatabaseName*的所有記錄項目。 
- 否則，就是資料庫 `DatabaseName` 和執行命令之使用者的所有記錄項目。 
