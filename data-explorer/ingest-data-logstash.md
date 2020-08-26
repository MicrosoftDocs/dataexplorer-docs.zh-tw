---
title: 將資料從 Logstash 擷取至 Azure 資料總管
description: 在本文中，您將瞭解如何從 Logstash 將) 資料內嵌 (載入 Azure 資料總管
author: orspod
ms.author: orspodek
ms.reviewer: takamara
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: b062b2da81662e89dc61720ebf6bb555f896a8ca
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874778"
---
# <a name="ingest-data-from-logstash-to-azure-data-explorer"></a>將資料從 Logstash 擷取至 Azure 資料總管

[Logstash](https://www.elastic.co/products/logstash) 是開放原始碼的伺服器端資料處理管線，可同時從許多來源擷取資料、轉換資料，然後將資料傳送至您最愛的「隱藏」。 在本文中，您會將該資料傳送至 Azure 資料總管，這是適用于記錄和遙測資料的快速且可高度調整的資料探索服務。 您將先在測試叢集中建立資料表和資料對應，然後指示 Logstash 將資料傳送至資料表，並驗證結果。

## <a name="prerequisites"></a>先決條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* Azure 資料總管[測試叢集和資料庫](create-cluster-database-portal.md)
* Logstash 第 6 版以上的[安裝指示](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html)

## <a name="create-a-table"></a>建立資料表

具備叢集和資料庫之後，即可建立資料表。

1. 在資料庫查詢視窗中執行下列命令，以建立資料表：

    ```Kusto
    .create table logs (timestamp: datetime, message: string)
    ```

1. 執行下列命令，確認新的資料表 `logs` 已建立，並且是空的：
    ```Kusto
    logs
    | count
    ```

## <a name="create-a-mapping"></a>建立對應

Azure 資料總管會使用對應將傳入的資料轉換成目標資料表結構描述。 下列命令會建立名為 `basicmsg` 的新對應，以依照 `path` 的指示從傳入的 Json 擷取屬性，並將其輸出至 `column`。

在查詢視窗中執行下列命令：

```Kusto
.create table logs ingestion json mapping 'basicmsg' '[{"column":"timestamp","path":"$.@timestamp"},{"column":"message","path":"$.message"}]'
```

## <a name="install-the-logstash-output-plugin"></a>安裝 Logstash 輸出外掛程式

Logstash 輸出外掛程式會與 Azure 資料總管通訊，並將資料傳送至服務。
請在 Logstash 根目錄內執行下列命令，以安裝外掛程式：

```sh
bin/logstash-plugin install logstash-output-kusto
```

## <a name="configure-logstash-to-generate-a-sample-dataset"></a>設定 Logstash 以產生範例資料集

Logstash 可產生用來測試端對端管線的範例事件。
如果您已使用 Logstash，並且可以存取您自己的事件串流，請跳至下一節。 

> [!NOTE]
> 如果要使用您自己的資料，請變更前幾個步驟中所定義的資料表和對應物件。

1. 編輯將包含必要管線設定的新文字檔 (使用 vi)：

    ```sh
    vi test.conf
    ```

1. 貼上下列設定，以指示 Logstash 產生 1000 個測試事件：

    ```ruby
    input {
        stdin { }
        generator {
            message => "Test Message 123"
            count => 1000
        }
    }
    ```

這項設定也包含 `stdin` 輸入外掛程式，此外掛程式可讓您自行撰寫更多訊息 (請務必使用 *Enter* 鍵將其提交至管線中)。

## <a name="configure-logstash-to-send-data-to-azure-data-explorer"></a>設定 Logstash 以將資料傳送至 Azure 資料總管

將下列設定貼到上一個步驟所使用的相同組態檔中。 請將所有預留位置都取代為設定的相關值。 如需詳細資訊，請參閱[建立 AAD 應用程式](kusto/management/access-control/how-to-provision-aad-app.md)。 

```ruby
output {
    kusto {
            path => "/tmp/kusto/%{+YYYY-MM-dd-HH-mm-ss}.txt"
            ingest_url => "https://ingest-<cluster name>.kusto.windows.net/"
            app_id => "<application id>"
            app_key => "<application key/secret>"
            app_tenant => "<tenant id>"
            database => "<database name>"
            table => "<target table>" # logs as defined above
            mapping => "<mapping name>" # basicmsg as defined above
    }
}
```

| 參數名稱 | 描述 |
| --- | --- |
| **path** | Logstash 外掛程式會先將事件寫入至暫存檔，再傳送至 Azure 資料總管。 此參數會包含應寫入檔案的路徑，以及檔案輪替在觸發上傳至 Azure 資料總管服務的動作時所使用的時間運算式。|
| **ingest_url** | 擷取相關通訊的 Kusto 端點。|
| **app_id**、**app_key** 和 **app_tenant**| 連線至 Azure 資料總管所需的認證。 請務必使用具有擷取權限的應用程式。 |
| **database**| 要放置事件的資料庫名稱。 |
| **table** | 要放置事件的目標資料表名稱。 |
| **對應** | 對應可用來將傳入事件 Json 字串對應至正確的資料列格式 (定義哪個屬性會進入哪個資料行)。 |

## <a name="run-logstash"></a>執行 Logstash

我們現在已準備就緒，可執行 Logstash 並測試我們的設定。

1. 在 Logstash 根目錄中，執行下列命令：

    ```sh
    bin/logstash -f test.conf
    ```

    您應該會看到列印在畫面上的資訊，然後看到我們的範例組態所產生的 1000 則訊息。 此時，您也可以手動輸入更多訊息。

1. 在幾分鐘後執行下列資料總管查詢，以在您定義的資料表中查看這些訊息：

    ```Kusto
    logs
    | order by timestamp desc
    ```

1. 選取 Ctrl+C 以結束 Logstash

## <a name="clean-up-resources"></a>清除資源

在資料庫中執行下列命令，以清除 `logs` 資料表：

```Kusto
.drop table logs
```

## <a name="next-steps"></a>後續步驟

* [撰寫查詢](write-queries.md)