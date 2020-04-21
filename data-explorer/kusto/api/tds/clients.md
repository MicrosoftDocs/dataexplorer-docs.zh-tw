---
title: MS-TDS 客戶端和庫斯特 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 MS-TDS 用戶端和 Kusto。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 233eec65c14d76b25b76cc85c7ddd190e5171dfe
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523489"
---
# <a name="ms-tds-clients-and-kusto"></a>MS-TDS 客戶端和函式庫斯特

Kusto 為 MS-SQL 客戶端實現 TDS 符合終結點。 由於相容性處於協定級別,因此任何庫或應用程式都可以使用 Azure 活動目錄身份驗證連接到 SQL Azure 資料庫,因此可以使用 Kusto 伺服器,以正交方式與作業系統或運行時使用。 只需像使用 SQL Azure 伺服器一樣使用 Kusto 伺服器功能變數名稱。

在 SQL 語言等級上,Kusto 實現 T-SQL 的子集和 SQL 伺服器模擬子集。 有關 SQL Server 實現 T-SQL 和 Kusto 之間的一些主要區別,請參閱[已知問題](./sqlknownissues.md)。

## <a name="net-sql-client"></a>.NET SQL 用戶端

Kusto 支援 SQL 用戶端的 Azure 活動目錄身份驗證。

有關詳細資訊,請參閱[.NET SQL 用戶端(使用者身份驗證)](./aad.md#net-sql-client-user)和[.NET SQL 用戶端(應用程式身份驗證)](./aad.md#net-sql-client-application)



## <a name="jdbc"></a>JDBC

Microsoft JDBC 驅動程式可用於使用 AAD 身份驗證連接到 Kusto。

使應用程式使用*mssql-jdbc* JAR 和*adal4j* JAR 及其所有可靠性的版本之一。 例如：

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

使用 JDBC 驅動程式類別`com.microsoft.sqlserver.jdbc.SQLServerDriver`

使用連接字串,如下所示:

```s
jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword
```

如果要使用 AAD 整合音取 auth 模式,請將*ActiveDirectory 密碼*取代為*ActiveDirectory 整合*

有關詳細資訊,請參閱[JDBC(使用者身份驗證)](./aad.md#jdbc-user)和[JDBC(應用程式身份驗證)](./aad.md#jdbc-application)

## <a name="odbc"></a>ODBC

支援 ODBC 的應用程式可以連接到庫索托。

建立 ODBC 資料來源:
1. 啟動 ODBC 資料來源管理員。
2. 建立新資料源(按鈕`Add`)。
3. 選取 `ODBC Driver 17 for SQL Server`。
3. 為資料來源指定名稱,並在`Server`欄位中指定 Kusto 叢集名稱,例如`mykusto.kusto.windows.net`
4. 選擇`Active Directory Integrated`驗證選項。
5. 下一個選項卡允許選擇資料庫。
7. 可以在以下選項卡中保留所有其他設置的預設值。
8. `Finish`按鈕將打開數據源摘要對話框,可在其中測試連接。
9. ODBC 源現在可用於應用程式。

如果 ODBC 應用程式可以接受連接字串,或者除 DSN 之外,請使用以下連接字串:
```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
```

某些 ODBC 應用程式不能很好地使用 NVARCHAR(MAX) 類型https://docs.microsoft.com/sql/relational-databases/native-client/features/using-large-value-types?view=sql-server-2017#sql-server-native-client-odbc-driver(有關詳細資訊,請參閱)。 用於此類應用的常見解決方法是將返回的數據強制轉換為 NVARCHAR(n),對於 n 具有一些值,例如 NVARCHAR(4000)。 這種解決方法不適用於 Kusto,因為 Kusto 只有一個字串類型,對於 SQL 用戶端,它被編碼為 NVARCHAR (MAX)。 Kusto 為此類應用提供了不同的解決方法。 可以將 Kusto 設定為透過連接字串將所有字串編碼為 NVARCHAR(n)。 連接字串的語言欄位可指定格式的調優選項: `language@OptionName1:OptionValue1,OptionName2:OptionValue2`。 

例如,以下連接字串將指示 Kusto 將字串編碼為 NVARCHAR(8000):
```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated,Language=any@MaxStringSize:8000"
```

### <a name="powershell"></a>PowerShell

使用 ODBC 驅動程式的 PowerShell 文稿範例:

```powershell
$conn = [System.Data.Common.DbProviderFactories]::GetFactory("System.Data.Odbc").CreateConnection()
$conn.ConnectionString = "Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
$conn.Open()
$conn.GetSchema("Tables")
$conn.Close()  

```



## <a name="linqpad"></a>LINQPad

可以通過連接到 Kusto 應用程式(就像 SQL 伺服器一樣)將 Linq 應用程式與 Kusto 一起使用。
LINQPad 可用於探索 Linq 相容性。 它還可用於流覽 Kusto 和執行 SQL 查詢。
LINQPad 是探索 Kusto TDS (SQL) 終結點的推薦工具。

像連接到 Microsoft SQL 伺服器一樣進行連接。 請注意,LINQPad 支援活動目錄身份驗證。

1. 按一下 [ `Add connection`]。
2. 選擇 `Build data context automatically`。
3. 選擇 LINQPad`Default (LINQ to SQL)`驅動程式 。
4. 為提供者選擇`SQL Azure`。
5. 對於伺服器指定 Kusto 叢集的名稱,例如`mykusto.kusto.windows.net`
6. 對登入可以`Windows Authentication (Active Directory)`選擇 。
7. 按一`Test`下 按鈕以驗證連接。
8. 按下`OK`按鈕。 瀏覽器窗口顯示包含資料庫的樹視圖。
9. 您可以瀏覽資料庫、表和列。
10. 在查詢視窗中,可以運行 SQL 查詢。 指定 SQL 語言並選取到資料庫的連線。
11. 在查詢視窗中還可以運行 LINQ 查詢。 例如,右鍵單擊瀏覽器視窗中的表。 選取`Count`選項。 讓它運行。

## <a name="azure-data-studio-134-and-above"></a>Azure 資料工作室(1.3.4 及以上)

1. 新連接。
2. 選擇連線型態`Microsoft SQL Server`: .
3. 將 Kusto 叢集名稱指定為伺服器名稱,例如`mykusto.kusto.windows.net`
4. 選擇認證類型: `Azure Active Directory - Universal with MFA support`.
5. 指定 AAD 中預配的帳戶,`myname@contoso.com`例如 (首次添加帳戶)。
6. 資料庫選取器可用於選擇資料庫。
7. `Connect`按鈕將帶您到資料庫儀錶板。
8. 右鍵按一下連接並`New Query`選擇 打開查詢選項卡或單擊儀錶`New Query`板上 的任務。

## <a name="power-bi-desktop"></a>Power BI Desktop

像連接到 SQL Azure 資料庫一樣進行連接。

1. 選擇`Get Data``More`中`Azure`,然後然後`Azure SQL Database`
2. 指定庫斯特托伺服器名稱,例如。`mykusto.kusto.windows.net`
3. 使用「直接查詢」 選項。
4. 選擇`Microsoft account`認證(不`Windows`),然後按一`sign in`下 。
5. 選取器顯示可用的資料庫。 繼續,就像使用真正的 SQL 伺服器一樣。

## <a name="excel"></a>Excel

像連接到 SQL Azure 資料庫一樣進行連接。

1. 在`Data`選項卡`Get Data`中`From Azure`,`From Azure SQL Database`
2. 指定庫斯特托伺服器名稱,例如。`mykusto.kusto.windows.net`
3. 選擇`Microsoft account`認證(不`Windows`),然後按一`sign in`下 。
4. 登入後,按`Connect`一下 。
5. 選取器顯示可用的資料庫。 繼續,就像使用真正的 SQL 伺服器一樣。

## <a name="tableau"></a>Tableau

1. 創建 ODBC 資料源,請參閱[ODBC](./clients.md#odbc)部分。
2. 連線`Other Databases (ODBC)`。
3. 在 中`DSN`選擇 ODBC 資料來源。
4. 使用`Connect`按鈕建立連接。
5. 一`Sign In`旦按鈕可用,登錄庫塞托。

## <a name="dbeaver-533-and-above"></a>DBeaver (5.3.3 及以上)

設定 DBeaver 以與 Kusto 相容的方式處理結果集:

1. 選擇`Window`選單中選擇`Preferences`。
2. 在`Editors`部份`Data Editor`中 選擇 。
3. 確保`Refresh data on next page reading`選中選項。

建立與 Kusto 資料庫的連線:

1. 建立新的資料庫連接(`Database`選單`New Connection`和選項)。
2. 尋找`Azure`並選擇`Azure SQL Database`。 按 `Next`。
3. 指定主機,例如`mykusto.kusto.windows.net`與資料庫,例如`mydatabase`。 不要將主資料庫名稱保留為資料庫名稱。 Kusto 需要連接到特定資料庫。
4. 此身份驗證選項,`Active Directory - Password`請選擇 。
5. 指定活動目錄使用者的認證,例如,`myname@contoso.com`以及此使用者的相應密碼。
6. 按`Test Connection …`以驗證連接詳細資訊是否正確。

## <a name="microsoft-sql-server-management-studio-v18x"></a>微軟 SQL 伺服器管理工作室 (v18.x)

1. 在`Object Explorer``Connect`中`Database Engine`, 中 。
2. 將 Kusto 叢集名稱指定為伺服器名稱,例如`mykusto.kusto.windows.net`
3. 使用`Active Directory - Integrated`選項進行身份驗證。
4. 按一下 [ `Options`]。
5. 在`Connect to database`組合中,您可以`Browse Server`通過 選項瀏覽可用的資料庫。
6. 單擊`Yes`以繼續流覽。
7. 該對話框顯示包含所有可用資料庫的樹視圖。 可以單擊一個繼續連接到資料庫。
8. 或者,在`Connect to database`組合中,可以選擇`default`。 按一下 [ `Connect`]。 物件資源管理員將顯示所有資料庫。
9. 目前不支援透過 SSMS 瀏覽資料庫物件,因為 SSMS 使用關聯的子查詢來瀏覽資料庫架構。 Kusto 不支援相關的子查詢,請參閱[相關子查詢](./sqlknownissues.md#correlated-sub-queries)。
10. 單擊資料庫。 按一`New Query`下 選項以打開查詢視窗。
11. 可以從查詢視窗執行自定義 SQL 查詢。

## <a name="matlab-via-jdbc"></a>MATLAB (透過 JDBC)

將所需的 JAR 檔添加到 MATLAB 的靜態類路徑的前面。 為此,請在首選項目錄中創建一個 *「javaclasspath.txt」* 檔案。 在 Matlab 的指令視窗執行以下指令: 

``` s
edit(fullfile(prefdir,'javaclasspath.txt'))
```

將完整路徑加入的 JAR 檔: 

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

> 在頂部>*之前*,您確實需要<才能將此檔添加到類路徑的前面。 進一步將"c:\full_path_to"替換為這些文件的實際完整路徑。 

重新啟動 MATLAB 以確保這些類實際上已載入。

在試著連線執行之前,執行以下指令 (MATLAB 命令視窗):

``` java
java.lang.System.clearProperty('javax.xml.transform.TransformerFactory')
```

> 這將*變形金剛工廠*重置為預設值(MATLAB 通常用撒克遜重載,但這與 ADAL4J 不相容)。

要連線到 Kusto TDS 終結點,請提交以下指令 (MATLAB 命令視窗):

```s
conn = database('<<KUSTO_DATABASE>>','<<AAD_USER>>','<<USER_PWD>>','com.microsoft.sqlserver.jdbc.SQLServerDriver',['jdbc:sqlserver://<<MYCLUSTER>>.kusto.windows.net:1433;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword;database='])
 ```

> 這裡是正確的,這隻是以 *"database_"* 結尾,然後沒有名稱,"資料庫"函數將自動將第一個輸入(資料庫名稱)追加到此字串中。
> 如果要使用 AAD 整合音取 auth 模式,請將*ActiveDirectory 密碼*取代為*ActiveDirectory 整合*

測試連接並運行範例查詢。 提交命令 (MATLAB 指令視窗) 的模片:

```s
data = select(conn, 'SELECT * FROM <<KUSTO_TABLE>>')
data
```

> 將*函式庫*托中的現有表取代KUSTO_TABLE



## <a name="sending-t-sql-queries-over-the-rest-api"></a>透過 REST API 傳送 T-SQL 查詢

[Kusto REST API](../rest/index.md)可以接受並執行 T-SQL 查詢。
此選項`csl`, 將要求傳送到查詢終結點,其中的屬性設定為 T-SQL 查詢本身的文字,`sql`[並將請求屬性](../netfx/request-properties.md)`OptionQueryLanguage`設定為值 。