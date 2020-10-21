---
title: 將資料從 Kafka 擷取至 Azure 資料總管
description: 在本文中，您將瞭解如何從 Kafka 將) 資料內嵌 (載入 Azure 資料總管。
author: orspod
ms.author: orspodek
ms.reviewer: ankhanol
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 8274cd04dc2ecf588bf4771c06e3f8a760cac74d
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92343159"
---
# <a name="ingest-data-from-apache-kafka-into-azure-data-explorer"></a>將 Apache Kafka 的資料內嵌至 Azure 資料總管
 
Azure 資料總管支援從[Apache Kafka](http://kafka.apache.org/documentation/)進行[資料](ingest-data-overview.md)內嵌。 Apache Kafka 是一種分散式串流平臺，用於建立即時串流資料管線，以在系統或應用程式之間可靠地移動資料。 [Kafka Connect](https://docs.confluent.io/3.0.1/connect/intro.html#kafka-connect) 是一種工具，可在 Apache Kafka 與其他資料系統之間進行可調整且可靠的資料串流處理。 Azure 資料總管 [Kafka 接收](https://github.com/Azure/kafka-sink-azure-kusto/blob/master/README.md) 可作為 Kafka 的連接器，而不需要使用程式碼。 從這個 [git](https://github.com/Azure/kafka-sink-azure-kusto/releases) 存放庫或 [Confluent 連接器中樞](https://www.confluent.io/hub/microsoftcorporation/kafka-sink-azure-kusto)下載接收器連接器 jar。

本文說明如何使用獨立的 Docker 安裝程式，將資料內嵌至 Azure 資料總管，以簡化 Kafka 叢集和 Kafka 連接器叢集的設定。 

如需詳細資訊，請參閱連接器 [git](https://github.com/Azure/kafka-sink-azure-kusto/blob/master/README.md) 存放庫和 [版本詳細](https://github.com/Azure/kafka-sink-azure-kusto/blob/master/README.md#13-major-version-specifics)資料。

## <a name="prerequisites"></a>先決條件

* 建立 [Microsoft Azure 帳戶](/azure/)。
* 安裝 [Azure CLI](/cli/azure/install-azure-cli)。
* 安裝 [Docker](https://docs.docker.com/get-docker/) 及 [Docker Compose](https://docs.docker.com/compose/install)。
* 使用預設快取和保留原則，[在 Azure 入口網站中建立 Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)。

## <a name="create-an-azure-active-directory-service-principal"></a>建立 Azure Active Directory 服務主體

Azure Active Directory 服務主體可透過 [Azure 入口網站](/azure/active-directory/develop/howto-create-service-principal-portal) 或以程式設計方式建立，如下列範例所示。

此服務主體將是連接器用來寫入 Azure 資料總管資料表的身分識別。 我們稍後將會授與此服務主體的許可權，以存取 Azure 資料總管。

1. 透過 Azure CLI 登入您的 Azure 訂用帳戶。 然後在瀏覽器中進行驗證。

   ```azurecli-interactive
   az login
   ```


1. 選擇您要用來執行實驗室的訂用帳戶。 當您有多個訂用帳戶時，需要此步驟。

   ```azurecli-interactive
   az account set --subscription YOUR_SUBSCRIPTION_GUID
   ```

1. 建立服務主體。 在此範例中，會呼叫服務主體 `kusto-kafka-spn` 。

   ```azurecli-interactive
   az ad sp create-for-rbac -n "kusto-kafka-spn"
   ```

1. 您將會收到 JSON 回應，如下所示。 複製 `appId` 、 `password` 和 `tenant` ，您將在稍後的步驟中需要它們。

    ```json
    {
      "appId": "fe7280c7-5705-4789-b17f-71a472340429",
      "displayName": "kusto-kafka-spn",
      "name": "http://kusto-kafka-spn",
      "password": "29c719dd-f2b3-46de-b71c-4004fb6116ee",
      "tenant": "42f988bf-86f1-42af-91ab-2d7cd011db42"
    }
    ```

## <a name="create-a-target-table-in-azure-data-explorer"></a>在 Azure 資料總管中建立目標資料表

1. 登入 [Azure 入口網站](https://portal.azure.com)

1. 移至您的 Azure 資料總管叢集中。

1. 使用下列命令建立名為的資料表 `Storms` ：

    ```kusto
    .create table Storms (StartTime: datetime, EndTime: datetime, EventId: int, State: string, EventType: string, Source: string)
    ```

    :::image type="content" source="media/ingest-data-kafka/create-table.png" alt-text="在 Azure 資料總管入口網站中建立資料表 ":::
    
1. `Storms_CSV_Mapping`使用下列命令，為內嵌資料建立對應的資料表對應：
    
    ```kusto
    .create table Storms ingestion csv mapping 'Storms_CSV_Mapping' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EventId","datatype":"int","Ordinal":2},{"Name":"State","datatype":"string","Ordinal":3},{"Name":"EventType","datatype":"string","Ordinal":4},{"Name":"Source","datatype":"string","Ordinal":5}]'
    ```    

1. 針對可設定的內嵌延遲，在資料表上建立批次內嵌原則。

    > [!TIP]
    > 內嵌 [批次處理原則](kusto/management/batchingpolicy.md) 是一種效能優化程式，其中包含三個參數。 第一個參數符合的觸發程式會內嵌到 Azure 資料總管資料表中。

    ```kusto
    .alter table Storms policy ingestionbatching @'{"MaximumBatchingTimeSpan":"00:00:15", "MaximumNumberOfItems": 100, "MaximumRawDataSizeMB": 300}'
    ```

1. 使用服務主體，從 [建立 Azure Active Directory 服務主體](#create-an-azure-active-directory-service-principal) ，以授與與資料庫搭配使用的許可權。

    ```kusto
    .add database YOUR_DATABASE_NAME admins  ('aadapp=YOUR_APP_ID;YOUR_TENANT_ID') 'AAD App'
    ```

## <a name="run-the-lab"></a>執行實驗室

下列實驗室的設計目的是讓您開始建立資料、設定 Kafka 連接器，以及使用連接器將此資料串流至 Azure 資料總管的體驗。 然後，您可以查看內嵌的資料。

### <a name="clone-the-git-repo"></a>複製 git 存放庫

複製實驗室的 git 存放[庫。](https://github.com/Azure/azure-kusto-labs)

1. 在您的電腦上建立本機目錄。

    ```
    mkdir ~/kafka-kusto-hol
    cd ~/kafka-kusto-hol
    ```

1. 複製存放庫。

    ```shell
    cd ~/kafka-kusto-hol
    git clone https://github.com/Azure/azure-kusto-labs
    cd azure-kusto-labs/kafka-integration/dockerized-quickstart
    ```

#### <a name="contents-of-the-cloned-repo"></a>複製之存放庫的內容

執行下列命令以列出複製之存放庫的內容：

```
cd ~/kafka-kusto-hol/azure-kusto-labs/kafka-integration/dockerized-quickstart
tree
```

這項搜尋的結果為：

```
├── README.md
├── adx-query.png
├── adx-sink-config.json
├── connector
│   └── Dockerfile
├── docker-compose.yaml
└── storm-events-producer
    ├── Dockerfile
    ├── StormEvents.csv
    ├── go.mod
    ├── go.sum
    ├── kafka
    │   └── kafka.go
    └── main.go
 ```

### <a name="review-the-files-in-the-cloned-repo"></a>檢查複製之存放庫中的檔案

下列各節說明上述檔案樹狀結構中檔案的重要部分。

#### <a name="adx-sink-configjson"></a>adx-sink-config.js開啟

此檔案包含您將在其中更新特定設定詳細資料的 Kusto 接收屬性檔案：
 
```json
{
    "name": "storm",
    "config": {
        "connector.class": "com.microsoft.azure.kusto.kafka.connect.sink.KustoSinkConnector",
        "flush.size.bytes": 10000,
        "flush.interval.ms": 10000,
        "tasks.max": 1,
        "topics": "storm-events",
        "kusto.tables.topics.mapping": "[{'topic': 'storm-events','db': '<enter database name>', 'table': 'Storms','format': 'csv', 'mapping':'Storms_CSV_Mapping'}]",
        "aad.auth.authority": "<enter tenant ID>",
        "aad.auth.appid": "<enter application ID>",
        "aad.auth.appkey": "<enter client secret>",
        "kusto.url": "https://ingest-<name of cluster>.<region>.kusto.windows.net",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "value.converter": "org.apache.kafka.connect.storage.StringConverter"
    }
}
```

根據您的 Azure 資料總管設定來取代下列屬性的值： `aad.auth.authority` 、 `aad.auth.appid` 、 `aad.auth.appkey` 、 `kusto.tables.topics.mapping` (資料庫名稱) 和 `kusto.url` 。

#### <a name="connector---dockerfile"></a>連接器-Dockerfile

此檔案具有可為連接器實例產生 docker 映射的命令。  它包含從 git 存放庫版本目錄下載的連接器。

#### <a name="storm-events-producer-directory"></a>風暴-事件-生產者目錄

此目錄具有 Go 程式，可讀取本機 "StormEvents.csv" 檔案，並將資料發佈至 Kafka 主題。

#### <a name="docker-composeyaml"></a>docker 撰寫. yaml

```yaml
version: "2"
services:
  zookeeper:
    image: debezium/zookeeper:1.2
    ports:
      - 2181:2181
  kafka:
    image: debezium/kafka:1.2
    ports:
      - 9092:9092
    links:
      - zookeeper
    depends_on:
      - zookeeper
    environment:
      - ZOOKEEPER_CONNECT=zookeeper:2181
      - KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092
  kusto-connect:
    build:
      context: ./connector
      args:
        KUSTO_KAFKA_SINK_VERSION: 1.0.1
    ports:
      - 8083:8083
    links:
      - kafka
    depends_on:
      - kafka
    environment:
      - BOOTSTRAP_SERVERS=kafka:9092
      - GROUP_ID=adx
      - CONFIG_STORAGE_TOPIC=my_connect_configs
      - OFFSET_STORAGE_TOPIC=my_connect_offsets
      - STATUS_STORAGE_TOPIC=my_connect_statuses
  events-producer:
    build:
      context: ./storm-events-producer
    links:
      - kafka
    depends_on:
      - kafka
    environment:
      - KAFKA_BOOTSTRAP_SERVER=kafka:9092
      - KAFKA_TOPIC=storm-events
      - SOURCE_FILE=StormEvents.csv
```

### <a name="start-the-containers"></a>啟動容器

1. 在終端機中，啟動容器：
    
    ```shell
    docker-compose up
    ```

    生產者應用程式將會開始將事件傳送至 `storm-events` 主題。 
    您應該會看到類似下列記錄的記錄：

    ```shell
    ....
    events-producer_1  | sent message to partition 0 offset 0
    events-producer_1  | event  2007-01-01 00:00:00.0000000,2007-01-01 00:00:00.0000000,13208,NORTH CAROLINA,Thunderstorm Wind,Public
    events-producer_1  | 
    events-producer_1  | sent message to partition 0 offset 1
    events-producer_1  | event  2007-01-01 00:00:00.0000000,2007-01-01 05:00:00.0000000,23358,WISCONSIN,Winter Storm,COOP Observer
    ....
    ```
    
1. 若要檢查記錄，請在不同的終端機中執行下列命令：

    ```shell
    docker-compose logs -f | grep kusto-connect
    ```
    
### <a name="start-the-connector"></a>啟動連接器

使用 Kafka Connect REST 呼叫來啟動連接器。

1. 在不同的終端機中，使用下列命令啟動接收工作：

    ```shell
    curl -X POST -H "Content-Type: application/json" --data @adx-sink-config.json http://localhost:8083/connectors
    ```
    
1. 若要檢查狀態，請在不同的終端機中執行下列命令：
    
    ```shell
    curl http://localhost:8083/connectors/storm/status
    ```

連接器會開始將內嵌程式排入至 Azure 資料總管。

> [!NOTE]
> 如果您有記錄連接器問題，請 [建立問題](https://github.com/Azure/kafka-sink-azure-kusto/issues)。

## <a name="query-and-review-data"></a>查詢和審核資料

### <a name="confirm-data-ingestion"></a>確認資料內嵌

1. 等候資料抵達 `Storms` 資料表。 若要確認資料傳輸，請檢查資料列計數：
    
    ```kusto
    Storms | count
    ```

1. 確認內嵌進程中沒有任何失敗：

    ```kusto
    .show ingestion failures
    ```
    
    一旦您看到資料，請嘗試一些查詢。 

### <a name="query-the-data"></a>查詢資料

1. 若要查看所有記錄，請執行下列 [查詢](write-queries.md)：
    
    ```kusto
    Storms
    ```

1. 使用 `where` 和 `project` 來篩選特定資料：
    
    ```kusto
    Storms
    | where EventType == 'Drought' and State == 'TEXAS'
    | project StartTime, EndTime, Source, EventId
    ```
    
1. 使用 [`summarize`](./write-queries.md#summarize) 運算子：

    ```kusto
    Storms
    | summarize event_count=count() by State
    | where event_count > 10
    | project State, event_count
    | render columnchart
    ```
    
    :::image type="content" source="media/ingest-data-kafka/kusto-query.png" alt-text="在 Azure 資料總管入口網站中建立資料表 ":::

如需更多查詢範例和指引，請參閱 [撰寫適用于 Azure 資料總管](write-queries.md) 和 [Kusto 查詢語言檔](./kusto/query/index.md)的查詢。

## <a name="reset"></a>重設

若要重設，請執行下列步驟：

1. 停止容器 (`docker-compose down -v`) 
1. 刪除 (`drop table Storms`)
1. 重新建立 `Storms` 資料表
1. 重新建立資料表對應
1. 重新開機容器 (`docker-compose up`) 

## <a name="clean-up-resources"></a>清除資源

若要刪除 Azure 資料總管資源，請使用 [az cluster delete](/cli/azure/kusto/cluster#az-kusto-cluster-delete) 或 [az Kusto database delete](/cli/azure/kusto/database#az-kusto-database-delete)：

```azurecli-interactive
az kusto cluster delete -n <cluster name> -g <resource group name>
az kusto database delete -n <database name> --cluster-name <cluster name> -g <resource group name>
```

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [Big data 架構](/azure/architecture/solution-ideas/articles/big-data-azure-data-explorer)。
* 瞭解 [如何將 JSON 格式的範例資料內嵌至 Azure 資料總管](./ingest-json-formats.md?tabs=kusto-query-language)。
* 針對其他 Kafka labs：
   * [實際操作實驗室，以分散式模式從 Confluent Cloud Kafka 內嵌](https://github.com/Azure/azure-kusto-labs/blob/master/kafka-integration/confluent-cloud/README.md)
   * [實際操作實驗室，以分散式模式從 HDInsight Kafka 內嵌](https://github.com/Azure/azure-kusto-labs/tree/master/kafka-integration/distributed-mode/hdinsight-kafka)
   * [實際操作實驗室，以分散式模式 AKS 的 Confluent IaaS Kafka 內嵌](https://github.com/Azure/azure-kusto-labs/blob/master/kafka-integration/distributed-mode/confluent-kafka/README.md)