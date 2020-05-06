---
title: MS-TDS 用戶端和 Kusto-Azure 資料總管
description: 本文說明在 Azure 資料總管中的 MS TDS 用戶端和 Kusto。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 5e2de0c29c58959ce683518b03bef9164fa9543c
ms.sourcegitcommit: 061eac135a123174c85fe1afca4d4208c044c678
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799623"
---
# <a name="ms-tds-clients-and-azure-data-explorer"></a>MS-TDS 用戶端和 Azure 資料總管

Azure 資料總管會針對 MS-SQL 用戶端執行與 TDS 相容的端點。 相容性是在通訊協定層級上。 任何可透過 Azure Active Directory （Azure AD）驗證連線到 SQL Azure 資料庫的程式庫或應用程式，都能與 Azure 資料總管伺服器搭配使用。 因此，您可以使用伺服器功能變數名稱，就像它是 SQL Azure 伺服器一樣。

Azure 資料總管會執行 T-sql 的子集和 SQL server 模擬的子集。 如需詳細資訊，請參閱 SQL Server 的 T-sql 和 Azure 資料總管的執行之間的差異[已知問題](./sqlknownissues.md)。

## <a name="net-sql-client"></a>.NET SQL 用戶端

Azure 資料總管支援 SQL 用戶端的 Azure AD 驗證。 如需詳細資訊，請參閱[.NET Sql 用戶端（使用者驗證）](./aad.md#net-sql-client-user)和[.Net sql 用戶端（應用程式驗證）](./aad.md#net-sql-client-application)

## <a name="jdbc"></a>JDBC

Microsoft JDBC driver 可用來透過 Azure AD authentication 連線到 Azure 資料總管。

建立應用程式以使用其中一個版本的*mssql-jdbc* JAR 和*adal4j* jar，及其所有相依性。
例如，

```s
mssql-jdbc-7.0.0.jre8.jar
adal4j-1.6.3.jar
accessors-smart-1.2.jar
activation-1.1.jar
asm-5.0.4.jar
commons-codec-1.11.jar
commons-lang3-3.5.jar
gson-2.8.0.jar
javax.mail-1.6.1.jar
jcip-annotations-1.0-1.jar
json-smart-2.3.jar
lang-tag-1.4.4.jar
nimbus-jose-jwt-6.5.jar
oauth2-oidc-sdk-5.64.4.jar
slf4j-api-1.7.21.jar
```

建立應用程式以使用 JDBC 驅動程式類別*SQLServerDriver*。

使用如下所示的連接字串。

```s
jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword
```

> [!NOTE]
> 如果您想要使用 Azure Active Directory 整合式驗證模式，請將*ActiveDirectoryPassword*取代為*ActiveDirectoryIntegrated*。 如需詳細資訊，請參閱[jdbc （使用者驗證）](./aad.md#jdbc-user)和[jdbc （應用程式驗證）](./aad.md#jdbc-application)。

## <a name="odbc"></a>ODBC

支援 ODBC 的應用程式可以連接到 Azure 資料總管。

建立 ODBC 資料來源：

1. 啟動 [ODBC 資料來源管理員]。
2. 選取 [**新增**] 以建立新的資料來源，並將*ODBC Driver 17 設定為 SQL Server*。
3. 提供資料來源的名稱，並在 [**伺服器**] 欄位中指定 Azure 資料總管叢集名稱。 例如， *mykusto.kusto.windows.net*。
4. 針對 [驗證] 選項，設定 [ **Active Directory 整合**]。
5. 選取 **[下一步]** 以設定資料庫。
7. 您可以在後面的索引標籤中，保留所有其他設定的預設值。
8. 選取 [**完成]** 以開啟 [資料來源摘要] 視窗，可在其中測試連接。

您現在可以將 ODBC 資料來源與應用程式搭配使用。

如果 ODBC 應用程式可以接受連接字串，而不是或除了 DSN，請使用下列各項。

```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
```

某些 ODBC 應用程式無法與`NVARCHAR(MAX)`類型搭配運作。 如需詳細資訊，請參閱https://docs.microsoft.com/sql/relational-databases/native-client/features/using-large-value-types?view=sql-server-2017#sql-server-native-client-odbc-driver。 

常見的因應措施是將傳回的資料轉換成*NVARCHAR （n）*，其中有一個值為 n。 例如， *NVARCHAR （4000）*。 不過，這種因應措施不適用於 Azure 資料總管，因為 Azure 資料總管只有一個字串類型，而 SQL 用戶端的編碼為*NVARCHAR （MAX）*。

Azure 資料總管提供不同的因應措施。 您可以設定 Azure 資料總管透過連接字串，將所有字串編碼為*NVARCHAR （n）* 。 連接字串中的 [語言] 欄位可以用來指定* language@OptionName1:OptionValue1OptionName2： OptionValue2*格式的微調選項。

例如，下列連接字串會指示 Azure 資料總管以*NVARCHAR （8000）* 編碼字串。

```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated,Language=any@MaxStringSize:8000"
```

### <a name="powershell"></a>PowerShell

以下是使用 ODBC 驅動程式的 PowerShell 腳本範例。

```powershell
$conn = [System.Data.Common.DbProviderFactories]::GetFactory("System.Data.Odbc").CreateConnection()
$conn.ConnectionString = "Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
$conn.Open()
$conn.GetSchema("Tables")
$conn.Close()  
```

## <a name="linqpad"></a>LINQPad

Linq 應用程式可與 Azure 資料總管搭配使用，方法是將它與 SQL server 連接。
使用 LINQPad 探索 Linq 相容性和流覽 Azure 資料總管。 它也可以執行 SQL 查詢，而且是探索 Azure 資料總管 TDS （SQL）端點的建議工具。

像您一樣地連接到 Microsoft SQL Server。 LINQPad 支援 Active Directory 驗證。

1. 選取 [新增連線]****。
2. **自動設定組建資料內容**。
3. 設定 LINQPad 驅動程式的**預設值（LINQ to SQL）**。
4. 設定**SQL Azure**。
5. 針對 [伺服器]，指定 Azure 資料總管叢集的名稱。 例如， *mykusto.kusto.windows.net*。
6. 設定**Windows 驗證（Active Directory）** 以進行登入。
7. 選取 [**測試**] 以確認連線能力。
8. 選取 [確定]  。 瀏覽器視窗會顯示含有資料庫的樹狀檢視。
9. 您可以流覽資料庫、資料表和資料行。
10. 您可以在查詢視窗中執行 SQL 查詢。 指定 SQL 語言，然後選取資料庫的連接。
11. 您也可以在查詢視窗中執行 LINQ 查詢。 例如，在瀏覽器視窗中選取資料表。 選取 [**計數**]，然後讓它執行。

## <a name="azure-data-studio-134-and-above"></a>Azure Data Studio （1.3.4 和更新版本）

建立新的連接。

1. 將 [連線類型] 設定為 [ **Microsoft SQL Server**]。
2. 指定 Azure 資料總管叢集的名稱做為伺服器名稱。 例如， *mykusto.kusto.windows.net*。
3. 將驗證類型設定為 [ **Azure Active Directory-通用] 和 [MFA 支援**]。
4. 指定在 Azure AD 中布建的帳戶。 例如： *myname@contoso.com* 。 第一次新增帳戶。
5. 使用 [資料庫選擇器] 選取資料庫。
6. 選取 [連線 **]** ，將您帶到資料庫儀表板，並設定連接。
7. 選取 [追加**查詢**] 以開啟查詢視窗，或選取儀表板上的 [追加**查詢**] 工作。

## <a name="power-bi-desktop"></a>Power BI Desktop

像您一樣，連接到 SQL Azure 資料庫。

1. 在 [**取得資料**] 底下，選取 [**更多**]。
2. 設定**Azure**，然後**Azure SQL Database**。
3. 指定 Azure 資料總管 server 名稱。 例如， *mykusto.kusto.windows.net*。
4. 選取 [ **DirectQuery**]。
5. 設定**Microsoft 帳戶**驗證（非**Windows**），然後選取 [登**入**]。
6. [資料庫選擇器] 會顯示可用的資料庫。 使用真正的 SQL server 繼續操作。

## <a name="excel"></a>Excel

像您一樣，連接到 SQL Azure 資料庫。

1. 在 [**資料**] 索引標籤下選取 [**取得資料**]，然後**從 [Azure** ] 和 [ **Azure SQL Database**] 設定。
2. 指定 Azure 資料總管 server 名稱。 例如， *mykusto.kusto.windows.net*。
3. 設定**Microsoft 帳戶**驗證（非**Windows**），然後選取 [登**入**]。
5. [資料庫選擇器] 會顯示可用的資料庫。 使用真正的 SQL server 繼續操作。
4. 登入後，選取 **[連線]**。
5. [資料庫選擇器] 會顯示可用的資料庫。 使用真正的 SQL server 繼續操作。

## <a name="tableau"></a>Tableau

建立 ODBC 資料來源。 如需詳細資訊，請參閱[ODBC](./clients.md#odbc)一節。

1. 透過**其他資料庫連接（ODBC）**。
2. 在**DSN**中設定 ODBC 資料來源。
3. 選取 **[連線]** 以建立連接。
4. 選取 [登**入**]，一旦按鈕可供使用，然後登入 Azure 資料總管。

## <a name="dbeaver-533-and-above"></a>DBeaver （5.3.3 和更新版本）

設定 DBeaver，以與 Azure 資料總管相容的方式來處理結果集。

1. 選取 [**視窗]** 功能表中的 [**喜好**設定]。
2. 選取 [**編輯器**] 區段中的 [**資料編輯器**]。
3. 請確定**下一頁讀取**的重新整理資料已標示。

建立 Azure 資料總管資料庫的連線。

1. 在 [**資料庫**] 功能表中選取 [**新增連接**]。
2. 尋找**Azure**並設定**Azure SQL Database**。 選取 [下一步]  。
3. 指定主機。 例如， *mykusto.kusto.windows.net*。
4. 指定資料庫。 例如， *mydatabase*。

> [!WARNING]
> 請勿使用*master*作為資料庫名稱。 Azure 資料總管需要與特定資料庫的連線。

5. 設定**Active Directory 密碼**以進行*驗證*。
6. 指定 active directory 使用者的認證。 例如， *myname@contoso.com*和會設定此使用者的對應密碼。
7. 選取 [**測試連接 ...** ] 確認連線詳細資料正確。

## <a name="microsoft-sql-server-management-studio-v18x"></a>Microsoft SQL Server Management Studio （v18 開始. x）

1. 選取 **[連線]**，然後在 [**物件總管**下**資料庫引擎**]。
2. 將 [Azure 資料總管叢集的名稱] 指定為 [伺服器名稱]。 例如， *mykusto.kusto.windows.net*。
3. 設定**Active Directory 整合**以進行驗證。
4. 選取 [**選項**]。
5. 選取 [連線**到資料庫**] 底下的 **[流覽伺服器**] 以流覽可用的資料庫。
6. 選取 **[是]** 以繼續流覽。
7. 此視窗會顯示樹狀檢視，其中包含所有可用的資料庫。 選取其中一個，以連接到資料庫。
8. 另一個可能性是選取 **[連接到資料庫]** 底下的 [**預設值**]，然後選取 **[連接]**。 [物件瀏覽器] 將會顯示所有資料庫。

> [!NOTE]
> 尚不支援透過 SSMS 流覽資料庫物件，因為 SSMS 會使用相互關聯子查詢來流覽資料庫架構。
> Azure 資料總管不支援相互關聯的子查詢。 如需詳細資訊，請參閱相互[關聯的子查詢](./sqlknownissues.md#correlated-sub-queries)。

10. 選取 [追加**查詢**] 以開啟查詢視窗，並設定您的資料庫。

您可以從查詢視窗執行自訂 SQL 查詢。

## <a name="matlab-via-jdbc"></a>MATLAB （透過 JDBC）

藉由在喜好設定目錄中建立 *"javaclasspath"* 檔案，將必要的 JAR 檔案新增至 MATLAB 的靜態路徑程式開頭。

1. 在 Matlab 的命令視窗中執行下列命令。

``` s
edit(fullfile(prefdir,'javaclasspath.txt'))
```

2. 將完整路徑新增至所需的 JAR-檔案。

``` s
<before>
c:\full\path\to\accessors-smart-1.2.jar
c:\full\path\to\activation-1.1.jar
c:\full\path\to\adal4j-1.6.3.jar
c:\full\path\to\asm-5.0.4.jar
c:\full\path\to\commons-codec-1.11.jar
c:\full\path\to\commons-lang3-3.5.jar
c:\full\path\to\gson-2.8.0.jar
c:\full\path\to\javax.mail-1.6.1.jar
c:\full\path\to\jcip-annotations-1.0-1.jar
c:\full\path\to\json-smart-2.3.jar
c:\full\path\to\lang-tag-1.4.4.jar
c:\full\path\to\mssql-jdbc-7.0.0.jre8.jar
c:\full\path\to\nimbus-jose-jwt-6.5.jar
c:\full\path\to\oauth2-oidc-sdk-5.64.4.jar
c:\full\path\to\slf4j-api-1.7.21.jar
```

> [!NOTE]
> 您需要在頂端>**之前**的 <，以便將這些檔案加入至路徑的前面。 此外，將*c:\full\path\to*取代為這些檔案的實際完整路徑。

3. 重新開機 MATLAB，以確定已載入這些類別。

4. 嘗試連接之前，請執行下列命令（MATLAB 命令視窗）。

``` java
java.lang.System.clearProperty('javax.xml.transform.TransformerFactory')
```

> [!NOTE]
> 這會將**TransformerFactory**重設為預設值（MATLAB 通常會使用**Saxon**將此多載，但這與**ADAL4J**不相容）。

5. 使用下列命令（MATLAB 命令視窗）連接到 Azure 資料總管 TDS 端點。

```s
conn = database('<<KUSTO_DATABASE>>','<<AAD_USER>>','<<USER_PWD>>','com.microsoft.sqlserver.jdbc.SQLServerDriver',['jdbc:sqlserver://<<MYCLUSTER>>.kusto.windows.net:1433;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword;database='])
 ```

> [!NOTE]
> 結尾是 **"database ="** ，而不是值。 *資料庫*函數會自動將第一個輸入（資料庫名稱）附加至這個字串。
> 如果您想要使用 Azure Active Directory 整合式驗證模式，請將*ActiveDirectoryPassword*取代為*ActiveDirectoryIntegrated*。

6. 測試連接並執行範例查詢。 提交下列命令（MATLAB 命令視窗）。

```s
data = select(conn, 'SELECT * FROM <<KUSTO_TABLE>>')
data
```

> [!NOTE]
> 將*KUSTO_TABLE*取代為 Azure 資料總管中現有的資料表。

## <a name="sending-t-sql-queries-over-the-rest-api"></a>透過 REST API 傳送 T-SQL 查詢

[Azure 資料總管 REST API](../rest/index.md)可以接受並執行 t-SQL 查詢。

1. 將要求傳送至查詢端點，並將 [ **csl** ] 屬性設定為 t-SQL 查詢的文字。
2. 將**[要求屬性](../netfx/request-properties.md)** **OptionQueryLanguage**設定為**sql**。
