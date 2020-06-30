---
title: 使用 Azure Cosmos 模擬器在本機開發
description: 您可以免費使用 Azure Cosmos 模擬器在本機開發及測試應用程式，不需建立 Azure 訂用帳戶。
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 01/31/2020
ms.openlocfilehash: 0a6a1bd5e56f94a97779a85eafd0d46473cc2d0c
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85262424"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>使用 Azure Cosmos 模擬器進行本機開發和測試

Azure Cosmos 模擬器提供一個模擬 Azure Cosmos DB 服務的本機環境做為開發之用。 您可以使用 Azure Cosmos 模擬器在本機開發及測試應用程式，不需建立 Azure 訂用帳戶，也不會產生任何費用。 如果您滿意應用程式在 Azure Cosmos 模擬器中的運作方式，就可以切換成使用雲端的 Azure Cosmos 帳戶。

您可以搭配 [SQL](local-emulator.md#sql-api)、[Cassandra](local-emulator.md#cassandra-api)、[MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb)、[Gremlin](local-emulator.md#gremlin-api) 和[資料表](local-emulator.md#table-api) API 帳戶使用 Azure Cosmos 模擬器進行開發。 不過，模擬器中的 [資料總管] 檢視目前僅完全支援 SQL API 的用戶端。 

## <a name="how-the-emulator-works"></a>模擬器的運作方式

Azure Cosmos 模擬器提供 Azure Cosmos DB 服務的高逼真度模擬。 它支援與 Azure Cosmos DB 完全相同的功能，包括支援建立和查詢資料、佈建和調整容器，以及執行預存程序和觸發程序。 您可以使用 Azure Cosmos 模擬器來開發及測試應用程式，並且只需對 Azure Cosmos DB 的連接端點進行單一設定變更，就能將應用程式部署到範圍遍及全球的 Azure。

雖然 Azure Cosmos DB 服務的模擬很可靠，但模擬器的實作會與服務有所不同。 例如，模擬器會使用標準的作業系統元件，比如使用本機檔案系統以獲得持續性，以及使用 HTTPS 通訊協定堆疊進行連線。 依賴 Azure 基礎結構的功能 (例如全域複寫、讀取/寫入的個位數毫秒延遲，以及可調整的一致性層級) 均不適用。

您可以使用 [Azure Cosmos DB 資料移轉工具](https://github.com/azure/azure-documentdb-datamigrationtool) \(英文\)，在 Azure Cosmos 模擬器與 Azure Cosmos DB 服務之間移轉資料。

您可以在 Windows Docker 容器上執行 Azure Cosmos 模擬器，請參閱 [Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) 以了解 docker pull 命令，以及 [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) 以了解 `Dockerfile` 和詳細資訊。

## <a name="differences-between-the-emulator-and-the-service"></a>模擬器和服務之間的差異

因為 Azure Cosmos 模擬器提供一個在本機開發人員工作站上執行的模擬環境，所以在模擬器和雲端 Azure Cosmos 帳戶之間有一些功能上的差異：

* 模擬器中的 [資料總管] 目前支援 SQL API 的用戶端。 [資料總管] 檢視和適用於 Azure Cosmos DB API (例如 MongoDB、資料表、圖表及 Cassandra API) 的作業並未受到完全支援。
* Azure Cosmos 模擬器僅支援單一固定帳戶及一個已知的主要金鑰。 Azure Cosmos 模擬器中無法重新產生金鑰，但您可以使用命令列選項來變更預設金鑰。
* Azure Cosmos 模擬器是一項無法擴充的服務，也不支援大量容器。
* Azure Cosmos 模擬器未提供不同的 [Azure Cosmos DB 一致性層級](consistency-levels.md)。
* Azure Cosmos 模擬器未提供[多重區域複寫](distribute-data-globally.md)。
* 因為 Azure Cosmos DB 服務最新的變更，您的 Azure Cosmos 模擬器複本可能不會一直是最新狀態，您應該參閱 [Azure Cosmos DB 容量規劃工具](https://www.documentdb.com/capacityplanner) \(英文\)，準確地評估應用程式的生產輸送量 (RU) 需求。
* 使用 Azure Cosmos 模擬器時，根據預設，您最多可以建立 25 個固定大小的容器 (僅支援使用 Azure Cosmos DB SDK)，或者，使用 Azure Cosmos 模擬器來建立 5 個無限制的容器。 如需變更此值的詳細資訊，請參閱[設定 PartitionCount 值](#set-partitioncount)。
* 模擬器支援的 id 屬性大小上限為 254 個字元。

## <a name="system-requirements"></a>系統需求

Azure Cosmos 模擬器的硬體和軟體需求如下：

* 軟體需求
  * Windows Server 2012 R2、Windows Server 2016 或 Windows 10
  * 64 位元作業系統
* 最低硬體需求
  * 2-GB RAM
  * 10-GB 可用硬碟空間

## <a name="installation"></a>安裝

您可以從 [Microsoft 下載中心](https://aka.ms/cosmosdb-emulator)下載並安裝 Azure Cosmos 模擬器，或在適用於 Windows 的 Docker 上執行模擬器。 如需在適用於 Window 的 Docker 使用模擬器的指示，請參閱[在 Docker 上執行](#running-on-docker)。

> [!NOTE]
> 若要安裝、設定和執行 Azure Cosmos 模擬器，您必須具備電腦的系統管理權限。 模擬器將建立/新增憑證，也會設定防火牆規則以便執行其服務；因此，模擬器必須能夠執行這類作業。

## <a name="running-on-windows"></a>在 Windows 上執行

若要啟動 Azure Cosmos 模擬器，請選取 [開始] 按鈕或按下 Windows 鍵。 開始輸入 **Azure Cosmos 模擬器**，然後從應用程式清單中選取模擬器。

![選取 [開始] 按鈕或按下 Windows 鍵，開始輸入 **Azure Cosmos 模擬器**，然後從應用程式清單中選取模擬器](./media/local-emulator/database-local-emulator-start.png)

如果模擬器正在執行，您就會在 Windows 工作列通知區域看到圖示。 ![Azure Cosmos DB 本機模擬器的工作列通知](./media/local-emulator/database-local-emulator-taskbar.png)

Azure Cosmos 模擬器預設會在連接埠 8081 上接聽的本機電腦 ("localhost") 上執行。

Azure Cosmos 模擬器預設會安裝到 `C:\Program Files\Azure Cosmos DB Emulator`。 您也可以從命令列啟動和停止模擬器。 如需詳細資訊，請參閱[命令列工具參考](#command-line)。

## <a name="start-data-explorer"></a>啟動資料總管

當 Azure Cosmos 模擬器啟動時，會自動在瀏覽器中開啟 Azure Cosmos 資料總管。 位址會顯示為 `https://localhost:8081/_explorer/index.html`。 如果您關閉資料總管且想要稍後重新開啟，可以在瀏覽器中開啟 URL，或從 Windows 系統匣圖示中的 Azure Cosmos 模擬器啟動它，如下所示。

:::image type="content" source="./media/local-emulator/database-local-emulator-data-explorer-launcher.png" alt-text="Azure Cosmos 本機模擬器的資料總管啟動程式":::

## <a name="checking-for-updates"></a>檢查更新

資料總管會表示是否有新的更新可供下載。

> [!NOTE]
> 在某個 Azure Cosmos 模擬器版本 (請參閱 %LOCALAPPDATA%\CosmosDBEmulator 或資料路徑選擇性設定) 中建立的資料，不保證可使用不同的版本加以存取。 如果您需要長期保存資料，建議您將資料儲存於 Azure Cosmos 帳戶，而不是 Azure Cosmos 模擬器。

## <a name="authenticating-requests"></a>驗證要求

就像雲端的 Azure Cosmos DB 一樣，您對 Azure Cosmos 模擬器所做的每個要求都必須經過驗證。 Azure Cosmos 模擬器支援使用單一固定帳戶及一個已知的驗證金鑰來進行主要金鑰驗證。 此帳戶和金鑰都是唯一允許搭配 Azure Cosmos 模擬器使用的認證， 其中包括：

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Azure Cosmos 模擬器所支援的主要金鑰僅能與模擬器搭配使用。 您無法將生產用的 Azure Cosmos DB 帳戶和金鑰與 Azure Cosmos 模擬器搭配使用。

> [!NOTE]
> 如果您使用 /Key 選項來啟動模擬器，則使用產生的金鑰，而非 `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`。 如需 /Key 選項的詳細資訊，請參閱[命令列工具參考](#command-line)。

就像 Azure Cosmos DB 一樣，Azure Cosmos 模擬器僅支援透過 TLS 的安全通訊。

## <a name="running-on-a-local-network"></a>在區域網路上執行

您可以在本機網路上執行模擬器。 若要啟用網路存取，請在[命令列](#command-line-syntax)上指定 `/AllowNetworkAccess` 選項，這也需要您指定 `/Key=key_string` 或 `/KeyFile=file_name`。 您可以使用 `/GenKeyFile=file_name`，預先產生具有隨機金鑰的檔案。 然後可將該檔案傳遞至 `/KeyFile=file_name` 或 `/Key=contents_of_file`。

若是第一次啟用網路存取，使用者應該關閉模擬器，並刪除模擬器的資料目錄 (%LOCALAPPDATA%\CosmosDBEmulator)。

## <a name="developing-with-the-emulator"></a>使用模擬器進行開發

### <a name="sql-api"></a>SQL API

在桌面上執行 Azure Cosmos 模擬器之後，就可以使用任何支援的 [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) 或 [Azure Cosmos DB REST API](/rest/api/cosmos-db/) 來與模擬器互動。 Azure Cosmos 模擬器也包含內建的資料總管，可讓您建立 SQL API 或適用於 Mongo DB API 的 Cosmos DB 的容器，並檢視及編輯項目，而不需撰寫任何程式碼。

```csharp
// Connect to the Azure Cosmos Emulator running locally
DocumentClient client = new DocumentClient(
   new Uri("https://localhost:8081"), "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>適用於 MongoDB 的 Azure Cosmos DB API

在桌面上執行 Azure Cosmos 模擬器之後，就可以使用[適用於 MongoDB 的 Azure Cosmos DB API](mongodb-introduction.md) 來與模擬器互動。 從命令提示字元中，以系統管理員身分使用 "/EnableMongoDbEndpoint" 來啟動模擬器。 然後使用下列連接字串來連線至 MongoDB API 帳戶：

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>資料表 API

在桌面上執行 Azure Cosmos 模擬器之後，就可以使用 [Azure Cosmos DB 資料表 API SDK](table-storage-how-to-use-dotnet.md) 來與模擬器互動。 從命令提示字元中，以系統管理員身分使用 "/EnableTableEndpoint" 來啟動模擬器。 接著，執行下列程式碼以連線到資料表 API 帳戶：

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>Cassandra API

從系統管理員的命令提示字元中，使用 "/EnableCassandraEndpoint" 來啟動模擬器。 或者，您也可以設定環境變數 `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`。

* [安裝 Python 2.7](https://www.python.org/downloads/release/python-2716/)

* [安裝 Cassandra CLI/CQLSH](https://cassandra.apache.org/download/)

* 在一般命令提示字元視窗中，執行下列命令：

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* 在 CQLSH 殼層中，執行下列命令以連線到 Cassandra 端點：

  ```bash
  CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
  DESCRIBE keyspaces;
  USE mykeyspace;
  CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
  INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
  SELECT * from table1;
  EXIT
  ```

### <a name="gremlin-api"></a>Gremlin API

從系統管理員的命令提示字元中，使用 "/EnableGremlinEndpoint" 來啟動模擬器。 或者，您也可以設定環境變數 `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

* [安裝 apache-tinkerpop-gremlin-console-3.3.4](https://archive.apache.org/dist/tinkerpop/3.3.4)。

* 在模擬器的資料總管中，建立資料庫 "db1" 和集合 "coll1"；針對分割區索引鍵，選擇 "/name"

* 在一般命令提示字元視窗中，執行下列命令：

  ```bash
  cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
  copy /y conf\remote.yaml conf\remote-localcompute.yaml
  notepad.exe conf\remote-localcompute.yaml
    hosts: [localhost]
    port: 8901
    username: /dbs/db1/colls/coll1
    password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    connectionPool: {
    enableSsl: false}
    serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
    config: { serializeResultToString: true  }}

  bin\gremlin.bat
  ```

* 在 Gremlin 殼層中，執行下列命令以連線到 Gremlin 端點：

  ```bash
  :remote connect tinkerpop.server conf/remote-localcompute.yaml
  :remote console
  :> g.V()
  :> g.addV('person1').property(id, '1').property('name', 'somename1')
  :> g.addV('person2').property(id, '2').property('name', 'somename2')
  :> g.V()
  ```

## <a name="export-the-tlsssl-certificate"></a>匯出 TLS/SSL 憑證

.NET 語言和執行階段使用 Windows 憑證存放區來安全地連線到 Azure Cosmos DB 本機模擬器。 其他語言則有自己管理和使用憑證的方法。 Java 使用自己的[憑證存放區](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)，而 Python 使用[通訊端包裝函式](https://docs.python.org/2/library/ssl.html)。

若要取得憑證來搭配未整合 Windows 憑證存放區的語言和執行階段使用，您必須使用 Windows 憑證管理員將它匯出。 您可以執行 certlm.msc 或依照[匯出 Azure Cosmos 模擬器憑證](./local-emulator-export-ssl-certificates.md)中的逐步指示來啟動它。 憑證管理員執行之後時，開啟 [個人憑證] \(如下所示)，並將有 "DocumentDBEmulatorCertificate" 易記名稱的憑證匯出為 BASE-64 編碼的 X.509 (.cer) 檔案。

![Azure Cosmos DB 本機模擬器的 TLS/SSL 憑證](./media/local-emulator/database-local-emulator-ssl_certificate.png)

遵循[新增憑證至 Java CA 憑證存放區](https://docs.microsoft.com/azure/java-add-certificate-ca-store)中的指示，X.509 憑證就可以匯入 Java 憑證存放區。 將憑證匯入至憑證存放區之後，SQL 和適用於 MongoDB 的 Azure Cosmos DB API 的用戶端就可以連線到 Azure Cosmos 模擬器。

從 Python 和 Node.js SDK 連接到模擬器時，會停用 TLS 驗證。

## <a name="command-line-tool-reference"></a><a id="command-line"></a>命令列工具參考
您可以從安裝位置使用命令列來啟動和停止模擬器、設定選項，以及執行其他作業。

### <a name="command-line-syntax"></a>命令列語法

    Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]

若要檢視選項清單，請在命令提示字元輸入 `Microsoft.Azure.Cosmos.Emulator.exe /?` 。

|**選項** | **說明** | **命令**| **引數**|
|---|---|---|---|
|[無引數] | 使用預設設定來啟動 Azure Cosmos 模擬器。 |Microsoft.Azure.Cosmos.Emulator.exe| |
|[說明] |顯示支援的命令列引數清單。|Microsoft.Azure.Cosmos.Emulator.exe /? | |
| GetStatus |取得 Azure Cosmos 模擬器的狀態。 狀態會以結束代碼表示：1 = 啟動，2 = 執行中，3 = 已停止。 負數的結束代碼表示發生錯誤。 不會產生其他輸出。 | Microsoft.Azure.Cosmos.Emulator.exe /GetStatus| |
| Shutdown| 關閉 Azure Cosmos 模擬器。| Microsoft.Azure.Cosmos.Emulator.exe /Shutdown | |
|資料路徑 | 指定用來儲存資料檔案的路徑。 預設值為 %LocalAppdata%\CosmosDBEmulator。 | Microsoft.Azure.Cosmos.Emulator.exe /DataPath=\<datapath\> | \<datapath\>:可存取的路徑 |
|連接埠 | 指定用於模擬器的連接埠號碼。 預設值為 8081。 |Microsoft.Azure.Cosmos.Emulator.exe /Port=\<port\> | \<port\>:單一連接埠號碼 |
| ComputePort | 指定用於計算 Interop 閘道服務的連接埠號碼。 閘道 HTTP 端點探查連接埠的計算方式為 ComputePort + 79。 因此，ComputePort 和 ComputePort + 79 必須開啟且可供使用。 預設值為 8900。 | Microsoft.Azure.Cosmos.Emulator.exe /ComputePort=\<computeport\> | \<computeport\>:單一連接埠號碼 |
| EnableMongoDbEndpoint=3.2 | 啟用 MongoDB API 3.2 | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.2 | |
| EnableMongoDbEndpoint=3.6 | 啟用 MongoDB API 3.6 | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.6 | |
| MongoPort | 指定要用於 MongoDB 相容性 API 的連接埠號碼。 預設值為 10255。 |Microsoft.Azure.Cosmos.Emulator.exe /MongoPort=\<mongoport\>|\<mongoport\>:單一連接埠號碼|
| EnableCassandraEndpoint | 啟用 Cassandra API | Microsoft.Azure.Cosmos.Emulator.exe /EnableCassandraEndpoint | |
| CassandraPort | 指定用於 Cassandra 端點的連接埠號碼。 預設值為 10350。 | Microsoft.Azure.Cosmos.Emulator.exe /CassandraPort=\<cassandraport\> | \<cassandraport\>:單一連接埠號碼 |
| EnableGremlinEndpoint | 啟用 Gremlin API | Microsoft.Azure.Cosmos.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort | 用於 Gremlin 端點的連接埠號碼。 預設值為 8901。 | Microsoft.Azure.Cosmos.Emulator.exe /GremlinPort=\<port\> | \<port\>:單一連接埠號碼 |
|EnableTableEndpoint | 啟用 Azure 資料表 API | Microsoft.Azure.Cosmos.Emulator.exe /EnableTableEndpoint | |
|TablePort | 用於 Azure 資料表端點的連接埠號碼。 預設值為 8902。 | Microsoft.Azure.Cosmos.Emulator.exe /TablePort=\<port\> | \<port\>:單一連接埠號碼|
| KeyFile | 從指定的檔案中讀取授權金鑰。 使用 /GenKeyFile 選項來產生金鑰檔 | Microsoft.Azure.Cosmos.Emulator.exe /KeyFile=\<file_name\> | \<file_name\>:檔案的路徑 |
| ResetDataPath | 以遞迴方式移除指定路徑中的所有檔案。 如果您未指定路徑，則會預設為 %LOCALAPPDATA%\CosmosDbEmulator | Microsoft.Azure.Cosmos.Emulator.exe /ResetDataPath=\<path> | \<path\>:檔案路徑  |
| StartTraces  |  使用 LOGMAN 開始收集偵錯追蹤記錄。 | Microsoft.Azure.Cosmos.Emulator.exe /StartTraces | |
| StopTraces     | 使用 LOGMAN 停止收集偵錯追蹤記錄。 | Microsoft.Azure.Cosmos.Emulator.exe /StopTraces  | |
| StartWprTraces  |  使用 Windows 效能記錄工具開始收集偵錯追蹤記錄。 | Microsoft.Azure.Cosmos.Emulator.exe /StartWprTraces | |
| StopWprTraces     | 使用 Windows 效能記錄工具停止收集偵錯追蹤記錄。 | Microsoft.Azure.Cosmos.Emulator.exe /StopWprTraces  | |
|FailOnSslCertificateNameMismatch | 根據預設，如果憑證的 SAN 不包含模擬器主機的網域名稱、本機 IPv4 位址、'localhost' 及 '127.0.0.1'，模擬器就會重新產生其自我簽署的 TLS/SSL 憑證。 使用此選項，模擬器反而將在啟動時失敗。 您接著應該使用 /GenCert 選項，來建立並安裝新的自我簽署 TLS/SSL 憑證。 | Microsoft.Azure.Cosmos.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | 產生並安裝新的自我簽署 TLS/SSL 憑證。 選擇性地包括其他 DNS 名稱的逗號分隔清單，以透過網路來存取模擬器。 | Microsoft.Azure.Cosmos.Emulator.exe /GenCert=\<dns-names\> |\<dns-names\>:其他 dns 名稱的選擇性逗號分隔清單  |
| DirectPorts |指定要用於直接連線的連接埠。 預設值為 10251、10252、10253、10254。 | Microsoft.Azure.Cosmos.Emulator.exe /DirectPorts:\<directports\> | \<directports\>:以逗號分隔的 4 個連接埠清單 |
| Key |模擬器的授權金鑰。 金鑰必須是 64 位元組向量的 base-64 編碼方式。 | Microsoft.Azure.Cosmos.Emulator.exe /Key:\<key\> | \<key\>:金鑰必須是 64 位元組向量的 base-64 編碼方式|
| EnableRateLimiting | 指定要啟用要求率限制行為。 |Microsoft.Azure.Cosmos.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |指定要停用要求率限制行為。 |Microsoft.Azure.Cosmos.Emulator.exe /DisableRateLimiting | |
| NoUI | 不要顯示模擬器使用者介面。 | Microsoft.Azure.Cosmos.Emulator.exe /NoUI | |
| NoExplorer | 啟動時不要顯示資料總管。 |Microsoft.Azure.Cosmos.Emulator.exe /NoExplorer | | 
| PartitionCount | 指定已分割容器的數目上限。 如需詳細資訊，請參閱[變更容器的數目](#set-partitioncount)。 | Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=\<partitioncount\> | \<partitioncount\>:允許的單一分割區容器數目上限。 預設值為 25。 允許的上限為 250。|
| DefaultPartitionCount| 指定已分割容器之分割區數目的預設值。 | Microsoft.Azure.Cosmos.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | \<defaultpartitioncount\> 預設值為 25。|
| AllowNetworkAccess | 讓模擬器可以透過網路存取。 您也必須傳遞 /Key=\<key_string\> 或 /KeyFile=\<file_name\> 才能啟用網路存取。 | Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=\<key_string\> 或 Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /KeyFile=\<file_name\>| |
| NoFirewall | 不要在使用 /AllowNetworkAccess 選項時調整防火牆規則。 |Microsoft.Azure.Cosmos.Emulator.exe /NoFirewall | |
| GenKeyFile | 產生新的授權金鑰並儲存到指定的檔案。 產生的金鑰可以搭配 /Key 或 /KeyFile 選項使用。 | Microsoft.Azure.Cosmos.Emulator.exe /GenKeyFile=\<path to key file\> | |
| 一致性 | 設定帳戶的預設一致性層級。 | Microsoft.Azure.Cosmos.Emulator.exe /Consistency=\<consistency\> | \<consistency\>:值必須屬於下列其中一個[一致性層級](consistency-levels.md)：Session、Strong、Eventual 或 BoundedStaleness。 預設值為 Session。 |
| ? | 顯示說明訊息。| | |

## <a name="change-the-number-of-containers"></a><a id="set-partitioncount"></a>變更容器數目

根據預設，您最多可以建立 25 個固定大小的容器 (僅支援使用 Azure Cosmos DB SDK)，或者，使用 Azure Cosmos 模擬器來建立 5 個無限制的容器。 藉由修改 **PartitionCount** 值，您最多可以建立 250 個固定大小的容器、50 個無限制的容器，或兩者不超過 250 個固定大小容器的任意組合 (其中一個無限制的容器 = 5 個固定大小的容器)。 但是，不建議將模擬器設定為搭配超過 200 個固定大小的容器來執行。 由於它對磁碟 IO 作業新增了額外負荷，因此，會在使用端點 API 時導致無法預期的逾時。

如果您在超過目前分割區計數之後嘗試建立容器，模擬器就會擲回 ServiceUnavailable 例外狀況，並隨附下列訊息。

「很抱歉，這個區域目前出現了大量的需求，此時無法完成您的需求。 我們正持續努力讓越來越多容量上線，並建議您再試一次。
ActivityId：12345678-1234-1234-1234-123456789abc"

若要變更 Azure Cosmos 模擬器中可用的容器數目，請執行下列步驟：

1. 以滑鼠右鍵按一下系統匣上的 [Azure Cosmos DB 模擬器] 圖示，然後按一下 [重設資料...]，來刪除所有的本機 Azure Cosmos 模擬器資料。
2. 刪除此資料夾 `%LOCALAPPDATA%\CosmosDBEmulator` 中的所有模擬器資料。
3. 結束所有開啟的執行個體，方法是以滑鼠右鍵按一下系統匣上的 [Azure Cosmos DB 模擬器] 圖示，然後按一下 [結束]。 結束所有執行個體可能需要數分鐘的時間。
4. 安裝最新版的 [Azure Cosmos 模擬器](https://aka.ms/cosmosdb-emulator)。
5. 啟動具有 PartitionCount 旗標的模擬器，方法是設定值 <= 250。 例如： `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100` 。

## <a name="controlling-the-emulator"></a>控制模擬器

模擬器隨附 PowerShell 模組，可用於啟動、停止、解除安裝及擷取服務的狀態。 執行下列 Cmdlet 以使用 PowerShell 模組：

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

或者，將 `PSModules` 目錄放置於您的 `PSModulesPath` 並匯入它，如下列命令所示：

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

以下是從 PowerShell 控制模擬器的命令摘要：

### `Get-CosmosDbEmulatorStatus`

**語法**

`Get-CosmosDbEmulatorStatus`

**備註**

傳回下列其中一個 ServiceControllerStatus 值：ServiceControllerStatus.StartPending、ServiceControllerStatus.Running 或 ServiceControllerStatus.Stopped。

### `Start-CosmosDbEmulator`

**語法**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**備註**

啟動模擬器。 根據預設，命令會等到模擬器準備好接受要求。 如果您希望啟動模擬器時立即傳回 Cmdlet，請使用 -NoWait 選項。

### `Stop-CosmosDbEmulator`

**語法**

 `Stop-CosmosDbEmulator [-NoWait]`

**備註**

停止模擬器。 根據預設，此命令會等到模擬器完全關機。 如果您希望模擬器開始關閉時立即傳回 Cmdlet，請使用 -NoWait 選項。

### `Uninstall-CosmosDbEmulator`

**語法**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**備註**

解除安裝模擬器，並選擇性地移除 $env 的完整內容：LOCALAPPDATA\CosmosDbEmulator。
Cmdlet 可確保在解除安裝之前停止模擬器。

## <a name="running-on-docker"></a>在 Docker 上執行

Azure Cosmos 模擬器可以在適用於 Windows 的 Docker 上執行。 模擬器無法在 Docker for Oracle Linux 上運作。

安裝 [Docker for Windows](https://www.docker.com/docker-windows) 後，以滑鼠右鍵按一下工具列上的 Docker 圖示，然後選取 [切換到 Windows 容器]，即可切換到 Windows 容器。

接下來，從最喜愛的殼層執行下列命令，即可從 Docker Hub 提取模擬器映像。

```bash
docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```
若要啟動映像，請執行下列命令。

從命令列執行：
```cmd

md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```

> [!NOTE]
> 如果您在執行 docker run 命令時看到連接埠衝突錯誤 (指定的連接埠已在使用中)，您可以藉由變更埠號碼來傳遞自訂連接埠。 例如，您可以將 "-p 8081:8081" 變更為 "-p 443:8081"

從 PowerShell：
```powershell

md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

```

回應如下所示：

```
Starting emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
```

現在，在您的用戶端使用回應中的端點和主要金鑰，並將 TLS/SSL 憑證匯入您的主機。 若要匯入 TLS/SSL 憑證，請從系統管理員命令提示字元中執行下列命令︰

從命令列執行：

```cmd
cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
powershell .\importcert.ps1
```

從 PowerShell：
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
.\importcert.ps1
```

模擬器啟動之後，關閉互動式殼層將會關閉模擬器的容器。

若要開啟資料總管，請在瀏覽器中瀏覽至下列 URL。 如上所示，回應訊息中提供模擬器端點。

    https://<emulator endpoint provided in response>/_explorer/index.html

如果您在 Linux Docker 容器上執行 .NET 用戶端應用程式，並且在主機電腦上執行 Azure Cosmos 模擬器，請依照下一節中關於 Linux 的指示，將憑證匯入 Linux Docker 容器中。

## <a name="running-on-mac-or-linux"></a>在 Mac 或 Linux 上執行<a id="mac"></a>

目前 Cosmos 模擬器只能在 Windows 上執行。 執行 Mac 或 Linux 的使用者可以在裝載於 Parallels 或 VirtualBox 等 Hypervisor 的 Windows 虛擬機器中執行模擬器。 以下是此功能的啟用步驟。

在 Windows VM 內執行下列命令，並記下 IPv4 位址。

```cmd
ipconfig.exe
```

您必須在應用程式內變更作為端點的 URI，以使用 `ipconfig.exe` (而非 `localhost`) 所傳回的 IPv4 位址。

然後，在 Windows VM 內，使用下列選項從命令列啟動 Cosmos 模擬器。

```cmd
Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

最後，我們必須將模擬器 CA 憑證匯入 Linux 或 Mac 環境中。

### <a name="linux"></a>Linux

如果您使用 Linux，則 .NET 會在 OpenSSL 上轉送以執行驗證：

1. [以 PFX 格式匯出憑證](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-tlsssl-certificate) (選擇匯出私密金鑰時，可以使用 PFX)。 

1. 將該 PFX 檔案複製到您的 Linux 環境中。

1. 將 PFX 檔案轉換為 CRT 檔案

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. 將 CRT 檔案複製到您的 Linux 發行版本中包含自訂憑證的資料夾。 其位置通常是在 Debian 發行版本的 `/usr/local/share/ca-certificates/` 上。

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. 更新 CA 憑證，這會更新 `/etc/ssl/certs/` 資料夾。

   ```bash
   update-ca-certificates
   ```

### <a name="mac-os"></a>Mac OS

如果您使用的是 Mac，請執行下列步驟：

1. [以 PFX 格式匯出憑證](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-tlsssl-certificate) (選擇匯出私密金鑰時，可以使用 PFX)。

1. 將該 PFX 檔案複製到您的 Mac 環境中。

1. 開啟*金鑰鏈存取*應用程式，並匯入 PFX 檔案。

1. 開啟憑證的清單，並找出名稱為 `localhost` 的憑證。

1. 開啟該特定項目的捷徑功能表，選取 [取得項目]，然後在 [信任] > [使用此憑證時] 選項底下，選取 [永遠信任]。 

   :::image type="content" source="./media/local-emulator/mac-trust-certificate.png" alt-text="開啟該特定項目的捷徑功能表，選取 [取得項目]，然後在 [信任 - 使用此憑證時] 選項底下，選取 [永遠信任]":::

執行這些步驟後，您的環境在連線至 `/AllowNetworkAccess` 所公開的 IP 位址時，將會信任模擬器所使用的憑證。

## <a name="troubleshooting"></a>疑難排解

使用下列秘訣，可協助您對使用 Azure Cosmos 模擬器時遇到的問題進行疑難排解：

- 如果您已安裝新版本的模擬器但發生了錯誤，請務必重設您的資料。 您可以滑鼠右鍵按一下系統匣上的 [Azure Cosmos 模擬器] 圖示，然後按一下 [重設資料...] 來重設資料。 如果該動作無法修正錯誤，您可以解除安裝模擬器以及任何所找到的舊版模擬器、移除 "C:\Program files\Azure Cosmos DB 模擬器" 目錄，然後重新安裝模擬器。 如需相關指示，請參閱[解除安裝本機模擬器](#uninstall)。

- 如果 Azure Cosmos 模擬器當機，請從 '%LOCALAPPDATA%\CrashDumps' 資料夾中收集傾印檔案並加以壓縮，然後從 [Azure 入口網站](https://portal.azure.com)開啟支援票證。

- 如果您在 `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe` 中遇到當機，這可能是效能計數器處於損毀狀態的徵兆。 通常從系統管理員的命令提示字元中執行下列命令，即可修正此問題：

  ```cmd
  lodctr /R
   ```

- 如果發生連線問題，請[收集追蹤檔案](#trace-files)並加以壓縮，然後在 [Azure 入口網站](https://portal.azure.com)中開啟支援票證。

- 如果您收到**服務無法使用**訊息，模擬器可能無法初始化網路堆疊。 由於 Pulse 安全用戶端或 Juniper 網路用戶端的網路篩選驅動程式可能會造成問題，因此請檢查是否已安裝這些驅動程式。 解除安裝協力廠商網路篩選驅動程式通常便會修正問題。 或者，使用 /DisableRIO 來啟動模擬器，將模擬器網路通訊切換為一般 Winsock。 

- 當模擬器執行時，如果您的電腦進入睡眠模式或執行任何作業系統更新，您應該會看見**服務目前無法使用**的訊息。 以滑鼠右鍵按一下視窗通知匣上出現的圖示，然後選取 [重設資料]，來重設模擬器的資料。

### <a name="collect-trace-files"></a><a id="trace-files"></a>收集追蹤檔案

若要收集偵錯追蹤，請從系統管理命令提示字元執行下列命令︰

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. 第 1 課：建立 Windows Azure 儲存體物件`Microsoft.Azure.Cosmos.Emulator.exe /shutdown`。 監看系統匣，確認程式已經關閉，這可能需要一分鐘的時間。 您也可以直接按一下 Azure Cosmos 模擬器使用者介面中的 [結束]。
3. `Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces`
4. `Microsoft.Azure.Cosmos.Emulator.exe`
5. 重現問題。 如果資料總管無法運作，您只需要等候數秒鐘的時間，等到瀏覽器開啟即可攔截錯誤。
6. `Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces`
7. 瀏覽至 `%ProgramFiles%\Azure Cosmos DB Emulator` 並尋找 docdbemulator_000001.etl 檔案。
8. 在 [Azure 入口網站](https://portal.azure.com)中開啟支援票證，並納入 .etl 檔案與重現步驟。

### <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>將本機模擬器解除安裝

1. 以滑鼠右鍵按一下系統匣上的 Azure Cosmos 模擬器圖示，然後按一下 [結束]，即可結束本機模擬器所有已開啟的執行個體。 結束所有執行個體可能需要數分鐘的時間。
2. 在 Windows 搜尋方塊中，輸入 **App 與功能**，然後按一下 [App 與功能 (系統設定)] 結果。
3. 在應用程式清單中，捲動至 [Azure Cosmos DB 模擬器] 並將其選取，按一下 [解除安裝]，然後確認並再按一下 [解除安裝]。
4. 當應用程式已解除安裝時，請瀏覽至 `%LOCALAPPDATA%\CosmosDBEmulator` 然後刪除該資料夾。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您學習到如何使用免費的本機模擬器在本機開發。 現在您可以繼續進行下一個教學課程，了解如何匯出模擬器 TLS/SSL 憑證。

> [!div class="nextstepaction"]
> [匯出 Azure Cosmos 模擬器憑證](local-emulator-export-ssl-certificates.md)
