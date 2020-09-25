---
title: 使用 Azure Cosmos DB 模擬器在本機安裝和開發
description: 瞭解如何在 Windows、Linux、macOS 和 Windows docker 環境上安裝和使用 Azure Cosmos DB 模擬器。 您可以使用模擬器在本機免費開發及測試應用程式，而不需建立 Azure 訂用帳戶。
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/22/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 07a38e106b765fd28a8c3c1115e5fe84744ade62
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91303084"
---
# <a name="install-and-use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>安裝和使用 Azure Cosmos 模擬器進行本機開發和測試

Azure Cosmos 模擬器提供一個模擬 Azure Cosmos DB 服務的本機環境，以供開發之用。 您可以使用 Azure Cosmos 模擬器在本機開發及測試應用程式，而不需建立 Azure 訂用帳戶，也不會產生任何費用。 當您滿意應用程式在 Azure Cosmos 模擬器中的運作方式時，您可以切換到使用雲端中的 Azure Cosmos 帳戶。 若要開始使用，請在本機電腦上下載並安裝最新版本的 [Azure Cosmos 模擬器](https://aka.ms/cosmosdb-emulator) 。 本文說明如何在 Windows、Linux、macOS 和 Windows docker 環境上安裝和使用模擬器。

您可以使用 Azure Cosmos 模擬器搭配 [SQL](local-emulator.md#sql-api)、 [Cassandra](local-emulator.md#cassandra-api)、 [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb)、 [Gremlin](local-emulator.md#gremlin-api)和 [資料表](local-emulator.md#table-api) API 帳戶來開發應用程式。 目前模擬器中的資料瀏覽器完全僅支援查看 SQL 資料;使用 MongoDB、Gremlin/Graph 及 Cassandra 用戶端應用程式建立的資料目前無法查看。 若要深入瞭解，請參閱如何從不同 [的 api 連接至模擬器端點](#connect-with-emulator-apis) 。

## <a name="how-does-the-emulator-work"></a>模擬器如何運作？

Azure Cosmos 模擬器提供 Azure Cosmos DB 服務的高精確度模擬。 它支援與 Azure Cosmos DB 相同的功能，包括建立資料、查詢資料、布建及調整容器，以及執行預存程式和觸發程式。 您可以使用 Azure Cosmos 模擬器來開發及測試應用程式，並藉由更新 Azure Cosmos DB 連線端點，以全球規模將它們部署到 Azure。

雖然 Azure Cosmos DB 服務的模擬很可靠，但模擬器的實作會與服務有所不同。 例如，模擬器會使用標準的作業系統元件，比如使用本機檔案系統以獲得持續性，以及使用 HTTPS 通訊協定堆疊進行連線。 當您使用模擬器時，依賴 Azure 基礎結構的功能（例如全域複寫、讀取/寫入的單一位數毫秒延遲，以及可調式的一致性層級）並不適用。

您可以使用 [Azure Cosmos DB 資料移轉工具](https://github.com/azure/azure-documentdb-datamigrationtool)，在 Azure Cosmos 模擬器和 Azure Cosmos DB 服務之間遷移資料。

## <a name="differences-between-the-emulator-and-the-cloud-service"></a>模擬器與雲端服務之間的差異

因為 Azure Cosmos 模擬器提供了在本機開發人員工作站上執行的模擬環境，所以模擬器和雲端中的 Azure Cosmos 帳戶之間有一些功能上的差異：

* 目前，模擬器中的 **資料總管** 窗格完全僅支援 SQL API 用戶端。 不完全支援 Azure Cosmos DB Api （例如 MongoDB、資料表、Graph 和 Cassandra Api）的 **資料總管** 查看和作業。

* 模擬器僅支援單一固定帳戶及已知的主要金鑰。 您無法在使用 Azure Cosmos 模擬器時重新產生金鑰，不過，您可以使用 [命令列](emulator-command-line-parameters.md) 選項來變更預設金鑰。

* 使用模擬器時，您只可以在布 [建的輸送量](set-throughput.md) 模式中建立 Azure Cosmos 帳戶;目前不支援 [無伺服器](serverless.md) 模式。

* 模擬器不是可擴充的服務，也不支援大量的容器。 使用 Azure Cosmos 模擬器時，根據預設，您最多可以在 400 RU/秒上建立25個固定大小的容器 (僅支援使用 Azure Cosmos DB Sdk) 或5個無限制的容器。 如需如何變更此值的詳細資訊，請參閱 [設定 PartitionCount 值] 模擬器-命令列參數. md # Set-PartitionCount) 文章。

* 模擬器不提供不同 Azure Cosmos DB 的 [一致性層級](consistency-levels.md) ，例如雲端服務。

* 模擬器不提供 [多重區域](distribute-data-globally.md)複寫。

* 由於您的 Azure Cosmos 模擬器複本可能不會與 Azure Cosmos DB 服務中最近的變更保持最新狀態，因此您應該一律參考 [Azure Cosmos DB 容量規劃](estimate-ru-with-capacity-planner.md) 工具，以精確地預估應用程式)  (ru 的輸送量。

* 模擬器支援的最大識別碼屬性大小為254個字元。

## <a name="install-the-emulator"></a>安裝模擬器

安裝模擬器之前，請確定您有下列硬體和軟體需求：

* 軟體需求：
  * 目前支援 Windows Server 2012 R2、Windows Server 2016、2019或 Windows 8、10個主機 OS。 目前不支援已啟用 Active Directory 的主機作業系統。
  * 64 位元作業系統

* 最低硬體需求：
  * 2-GB RAM
  * 10-GB 可用硬碟空間

* 若要安裝、設定及執行 Azure Cosmos 模擬器，您必須具備電腦的系統管理許可權。 模擬器會新增憑證，也會設定防火牆規則，以便執行其服務。 因此，模擬器必須具備系統管理員許可權，才能執行這類作業。

若要開始使用，請在本機電腦上下載並安裝最新版本的 [Azure Cosmos 模擬器](https://aka.ms/cosmosdb-emulator) 。 如果您在安裝模擬器時遇到任何問題，請參閱 [模擬器疑難排解](troubleshoot-local-emulator.md) 文章以進行 debug。

視您的系統需求而定，您可以在 [Windows](#run-on-windows)、 [適用於 Windows 的 Docker](#run-on-windows-docker)、[Linux 或 [macOS](#run-on-linux-macos) 上執行模擬器，如下一節中所述。

## <a name="check-for-emulator-updates"></a>檢查模擬器更新

每個版本的模擬器都有一組功能更新或錯誤修正。 若要查看可用的版本，請閱讀 [模擬器版本](local-emulator-release-notes.md) 資訊文章。

安裝之後，如果您已使用預設設定，對應至模擬器的資料會儲存在%LOCALAPPDATA%\CosmosDBEmulator 位置。 您可以使用選擇性的資料路徑設定來設定不同的位置;這是 `/DataPath=PREFERRED_LOCATION` 做為 [命令列參數](emulator-command-line-parameters.md)。 使用不同的版本時，不保證可存取在某個 Azure Cosmos 模擬器版本中建立的資料。 如果您需要長期保存資料，建議您將該資料儲存在 Azure Cosmos 帳戶中，而不是 Azure Cosmos 模擬器。

## <a name="use-the-emulator-on-windows"></a><a id="run-on-windows"></a>在 Windows 上使用模擬器

預設會在位置安裝 Azure Cosmos 模擬器 `C:\Program Files\Azure Cosmos DB Emulator` 。 若要在 Windows 上啟動 Azure Cosmos 模擬器，請選取 [ **開始** ] 按鈕或按下 windows 鍵。 開始輸入 **Azure Cosmos 模擬器**，然後從應用程式清單中選取模擬器。

:::image type="content" source="./media/local-emulator/database-local-emulator-start.png" alt-text="選取 [開始] 按鈕或按 Windows 鍵，開始鍵入 Azure Cosmos 模擬器，然後從應用程式清單中選取模擬器":::

模擬器啟動時，您會在 Windows 工作列通知區域中看到一個圖示。 它會在您的瀏覽器中自動開啟 Azure Cosmos data explorer，網址是 url `https://localhost:8081/_explorer/index.html` 。

:::image type="content" source="./media/local-emulator/database-local-emulator-taskbar.png" alt-text="Azure Cosmos DB 本機模擬器的工作列通知":::

您也可以從命令列或 PowerShell 命令啟動和停止模擬器。 如需詳細資訊，請參閱 [命令列工具參考](emulator-command-line-parameters.md) 文章。

Azure Cosmos 模擬器預設會在本機電腦上執行 ( "localhost" ) 接聽埠8081。 位址會顯示為 `https://localhost:8081/_explorer/index.html`。 如果您關閉瀏覽器，並想要稍後重新開啟它，您可以在瀏覽器中開啟 URL，或從 Windows 系統匣圖示中的 Azure Cosmos 模擬器啟動該 URL，如下所示。

:::image type="content" source="./media/local-emulator/database-local-emulator-data-explorer-launcher.png" alt-text="Azure Cosmos 本機模擬器的資料總管啟動程式":::

## <a name="use-the-emulator-on-docker-for-windows"></a><a id="run-on-windows-docker"></a>在適用於 Windows 的 Docker 上使用模擬器

您可以在 Windows Docker 容器上執行 Azure Cosmos 模擬器。 如需詳細資訊，請參閱 docker pull 命令和[GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker)的[Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) `Dockerfile` 。 模擬器目前無法在 Docker 上適用于 Oracle Linux。 使用下列指示，在適用於 Windows 的 Docker 上執行模擬器：

1. 安裝 [適用於 Windows 的 Docker](https://www.docker.com/docker-windows) 之後，請以滑鼠右鍵按一下工具列上的 Docker 圖示，然後選取 [ **切換至 windows 容器**]，切換至 windows 容器。

1. 接下來，從最喜愛的殼層執行下列命令，即可從 Docker Hub 提取模擬器映像。

   ```bash
   docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```

1. 若要啟動映射，請視命令列或 PowerShell 環境而定，執行下列命令：

   # <a name="command-line"></a>[命令列](#tab/cli)

   ```bash

   md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```
   以 windows 為基礎的 Docker 映射可能不會與每個 Windows 主機作業系統相容。 例如，預設的 Azure Cosmos 模擬器映射僅與 Windows 10 和 Windows Server 2016 相容。 如果您需要與 Windows Server 2019 相容的映射，請改為執行下列命令：

   ```bash
   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%hostDirectory%,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/winsrv2019/azure-cosmos-emulator:latest
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell

   md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

   ```

   回應如下所示：

   ```bash
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
   ---

   > [!NOTE]
   > 執行命令時 `docker run` ，如果您看到埠衝突錯誤 (亦即，如果指定的埠已在使用中) ，請修改埠號碼以傳遞自訂埠。 例如，您可以將 "-p 8081:8081" 參數變更為 "-p 443:8081"

1. 現在使用回應中的模擬器端點和主要金鑰，然後將 TLS/SSL 憑證匯入您的主機。 若要匯入 TLS/SSL 憑證，請從系統管理員命令提示字元執行下列步驟：

   # <a name="command-line"></a>[命令列](#tab/cli)

   ```bash
   cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
   powershell .\importcert.ps1
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell
   cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
   .\importcert.ps1
   ```
   ---

1. 如果您在模擬器啟動之後關閉互動式 shell，它將會關閉模擬器的容器。 若要重新開啟資料 explorer，請在您的瀏覽器中流覽至下列 URL。 如上所示，回應訊息中提供模擬器端點。

   `https://<emulator endpoint provided in response>/_explorer/index.html`

如果您在 Linux docker 容器上執行 .NET 用戶端應用程式，並在主機電腦上執行 Azure Cosmos 模擬器，請使用下一節中的指示，將憑證匯入至 Linux docker 容器。

### <a name="regenerate-the-emulator-certificates-when-running-on-a-docker-container"></a>在 Docker 容器上執行時重新產生模擬器憑證

在 Docker 容器中執行模擬器時，每次您停止並重新啟動個別的容器時，就會重新產生與模擬器相關聯的憑證。 因此，在每個容器啟動之後，您必須重新匯入憑證。 若要解決這項限制，您可以使用 Docker 撰寫檔案，將 Docker 容器系結至特定的 IP 位址和容器映射。

例如，您可以在 Docker 撰寫檔案中使用下列設定，請務必依據您的需求將它格式化： 

```yml
version: '2.4' # Do not upgrade to 3.x yet, unless you plan to use swarm/docker stack: https://github.com/docker/compose/issues/4513

networks:
  default:
    external: false
    ipam:
      driver: default
      config:
        - subnet: "172.16.238.0/24"

services:

  # First create a directory that will hold the emulator traces and certificate to be imported
  # set hostDirectory=C:\emulator\bind-mount
  # mkdir %hostDirectory%

  cosmosdb:
    container_name: "azurecosmosemulator"
    hostname: "azurecosmosemulator"
    image: 'mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator'
    platform: windows
    tty: true
    mem_limit: 3GB
    ports:
        - '8081:8081'
        - '8900:8900'
        - '8901:8901'
        - '8902:8902'
        - '10250:10250'
        - '10251:10251'
        - '10252:10252'
        - '10253:10253'
        - '10254:10254'
        - '10255:10255'
        - '10256:10256'
        - '10350:10350'
    networks:
      default:
        ipv4_address: 172.16.238.246
    volumes:
        - '${hostDirectory}:C:\CosmosDB.Emulator\bind-mount'
```

## <a name="use-the-emulator-on-linux-or-macos"></a><a id="run-on-linux-macos"></a>在 Linux 或 macOS 上使用模擬器

您目前只能在 Windows 上執行 Azure Cosmos 模擬器。 如果您使用 Linux 或 macOS，您可以在裝載于執行程式的 Windows 虛擬機器中執行模擬器，例如平行或 VirtualBox。

> [!NOTE]
> 每次重新開機裝載于管理程式的 Windows 虛擬機器時，您都必須重新匯入憑證，因為虛擬機器的 IP 位址會變更。 如果您已將虛擬機器設定為保留 IP 位址，則不需要匯入憑證。

使用下列步驟，在 Linux 或 macOS 環境上使用模擬器：

1. 從 Windows 虛擬機器執行下列命令，並記下 IPv4 位址：

   ```bash
   ipconfig.exe
   ```

1. 在您的應用程式中，將端點 URL 變更為使用傳回的 IPv4 位址， `ipconfig.exe` 而不是 `localhost` 。

1. 從 Windows VM，使用下列選項從命令列啟動 Azure Cosmos 模擬器。 如需命令列所支援參數的詳細資訊，請參閱 [模擬器命令列工具參考](emulator-command-line-parameters.md)：

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM +4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   ```

1. 最後，您必須在 Linux 或 Mac 環境與模擬器上執行的應用程式之間，解決憑證信任程式。 您可以使用下列兩個選項的其中一個來解析憑證：

   1. 將[模擬器 TLS/SSL 憑證匯入至 Linux 或 Mac 環境](#import-certificate)或
   2. [停用應用程式中的 TLS/SSL 驗證](#disable-ssl-validation)

### <a name="option-1-import-the-emulator-tlsssl-certificate"></a><a id="import-certificate"></a>選項1：匯入模擬器 TLS/SSL 憑證

下列各節說明如何將模擬器 TLS/SSL 憑證匯入至 Linux 和 macOS 環境。

#### <a name="linux-environment"></a>Linux 環境

如果您使用 Linux，則 .NET 會在 OpenSSL 上轉送以執行驗證：

1. [以 PFX 格式匯出憑證](local-emulator-export-ssl-certificates.md#export-emulator-certificate)。 選擇匯出私密金鑰時，可以使用 PFX 選項。

1. 將該 PFX 檔案複製到您的 Linux 環境中。

1. 將 PFX 檔案轉換為 CRT 檔案

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. 將 CRT 檔案複製到您的 Linux 發行版本中包含自訂憑證的資料夾。 其位置通常是在 Debian 發行版本的 `/usr/local/share/ca-certificates/` 上。

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. 更新將會更新資料夾的 TLS/SSL 憑證 `/etc/ssl/certs/` 。

   ```bash
   update-ca-certificates
   ```

#### <a name="macos-environment"></a>macOS 環境

如果您使用的是 Mac，請執行下列步驟：

1. [以 PFX 格式匯出憑證](local-emulator-export-ssl-certificates.md#export-emulator-certificate)。 選擇匯出私密金鑰時，可以使用 PFX 選項。

1. 將該 PFX 檔案複製到您的 Mac 環境中。

1. 開啟*金鑰鏈存取*應用程式，並匯入 PFX 檔案。

1. 開啟憑證的清單，並找出名稱為 `localhost` 的憑證。

1. 開啟該特定項目的捷徑功能表，選取 [取得項目]，然後在 [信任] > [使用此憑證時] 選項底下，選取 [永遠信任]。 

   :::image type="content" source="./media/local-emulator/mac-trust-certificate.png" alt-text="開啟該特定項目的捷徑功能表，選取 [取得項目]，然後在 [信任 - 使用此憑證時] 選項底下，選取 [永遠信任]":::
  
### <a name="option-2-disable-the-ssl-validation-in-the-application"></a><a id="disable-ssl-validation"></a>選項2：停用應用程式中的 SSL 驗證

基於開發目的，只建議停用 SSL 驗證，且不應在生產環境中執行時進行。 下列範例示範如何停用 .NET 和 Node.js 應用程式的 SSL 驗證。

# <a name="net-standard-21"></a>[.NET Standard 2.1 +](#tab/ssl-netstd21)

針對在與 .NET Standard 2.1 或更新版本相容的架構中執行的任何應用程式，我們可以利用 `CosmosClientOptions.HttpClientFactory` ：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard21)]

# <a name="net-standard-20"></a>[.NET Standard 2.0](#tab/ssl-netstd20)

針對在與 .NET Standard 2.0 相容的架構中執行的任何應用程式，我們可以利用 `CosmosClientOptions.HttpClientFactory` ：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard20)]

# <a name="nodejs"></a>[Node.js](#tab/ssl-nodejs)

針對 Node.js 的應用程式，您可以在 `package.json` `NODE_TLS_REJECT_UNAUTHORIZED` 啟動應用程式時修改您的檔案，以設定：

```json
"start": NODE_TLS_REJECT_UNAUTHORIZED=0 node app.js
```
--- 

## <a name="enable-access-to-emulator-on-a-local-network"></a>在區域網路上啟用模擬器的存取

如果您有多部電腦使用單一網路，而且您在一部電腦上設定模擬器，而且想要從其他電腦存取該模擬器。 在這種情況下，您必須在區域網路上啟用對模擬器的存取。

您可以在本機網路上執行模擬器。 若要啟用網路存取，請在[命令列](emulator-command-line-parameters.md)上指定 `/AllowNetworkAccess` 選項，這也需要您指定 `/Key=key_string` 或 `/KeyFile=file_name`。 您可以使用 `/GenKeyFile=file_name`，預先產生具有隨機金鑰的檔案。 然後可將該檔案傳遞至 `/KeyFile=file_name` 或 `/Key=contents_of_file`。

若要在第一次啟用網路存取，使用者應該關閉模擬器，並刪除模擬器的資料目錄 *%LOCALAPPDATA%\CosmosDBEmulator*。

## <a name="authenticate-connections-when-using-emulator"></a><a id="authenticate-requests"></a>使用模擬器時驗證連接

如同雲端中的 Azure Cosmos DB，您對 Azure Cosmos 模擬器提出的每個要求都必須經過驗證。 Azure Cosmos 模擬器僅支援透過 TLS 的安全通訊。 Azure Cosmos 模擬器支援主要金鑰驗證的單一固定帳戶和知名驗證金鑰。 此帳戶和金鑰都是唯一允許搭配 Azure Cosmos 模擬器使用的認證， 其中包括：

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Azure Cosmos 模擬器所支援的主要金鑰僅供模擬器使用。 您無法將生產用的 Azure Cosmos DB 帳戶和金鑰與 Azure Cosmos 模擬器搭配使用。

> [!NOTE]
> 如果您已使用/Key 選項啟動模擬器，則請使用產生的金鑰，而不是預設的金鑰 `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` 。 如需 /Key 選項的詳細資訊，請參閱[命令列工具參考](emulator-command-line-parameters.md)。

## <a name="connect-to-different-apis-with-the-emulator"></a><a id="connect-with-emulator-apis"></a>使用模擬器連接到不同的 Api

### <a name="sql-api"></a>SQL API

當您在桌面上執行 Azure Cosmos 模擬器之後，就可以使用任何支援的 [AZURE COSMOS DB SDK](sql-api-sdk-dotnet-standard.md) 或 [Azure Cosmos DB REST API](/rest/api/cosmos-db/) 來與模擬器互動。 Azure Cosmos 模擬器也包含內建的資料瀏覽器，可讓您建立適用于 SQL API 的容器或適用于 Mongo DB API 的 Azure Cosmos DB。 您可以使用 [資料瀏覽器] 來查看和編輯專案，而不需要撰寫任何程式碼。

```csharp
// Connect to the Azure Cosmos emulator running locally
CosmosClient client = new CosmosClient(
   "https://localhost:8081", 
    "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>適用於 MongoDB 的 Azure Cosmos DB API

當您在桌面上執行 Azure Cosmos 模擬器之後，就可以使用 [Azure Cosmos DB 的 MONGODB API](mongodb-introduction.md) 來與模擬器互動。 以具有 "/EnableMongoDbEndpoint" 的系統管理員身分從 [命令提示](emulator-command-line-parameters.md) 字元啟動模擬器。 然後使用下列連接字串來連線至 MongoDB API 帳戶：

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>資料表 API

當您在桌面上執行 Azure Cosmos 模擬器之後，就可以使用 [Azure Cosmos DB 資料表 API SDK](table-storage-how-to-use-dotnet.md) 來與模擬器互動。 以具有 "/EnableTableEndpoint" 的系統管理員身分從 [命令提示](emulator-command-line-parameters.md) 字元啟動模擬器。 接著，執行下列程式碼以連線到資料表 API 帳戶：

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

從系統管理員 [命令提示](emulator-command-line-parameters.md) 字元使用 "/EnableCassandraEndpoint" 啟動模擬器。 或者，您也可以設定環境變數 `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`。

1. [安裝 Python 2.7](https://www.python.org/downloads/release/python-2716/)

1. [安裝 Cassandra CLI/CQLSH](https://cassandra.apache.org/download/)

1. 在一般命令提示字元視窗中，執行下列命令：

   ```bash
   set Path=c:\Python27;%Path%
   cd /d C:\sdk\apache-cassandra-3.11.3\bin
   set SSL_VERSION=TLSv1_2
   set SSL_VALIDATE=false
   cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
   ```

1. 在 CQLSH 殼層中，執行下列命令以連線到 Cassandra 端點：

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

從系統管理員 [命令提示](emulator-command-line-parameters.md)字元使用 "/EnableGremlinEndpoint" 啟動模擬器。 或者，您也可以設定環境變數 `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

1. [安裝 apache-tinkerpop-gremlin-console-3.3.4](https://archive.apache.org/dist/tinkerpop/3.3.4)。

1. 從模擬器的資料 explorer 建立資料庫 "db1" 和集合 "coll1";針對分割區索引鍵，選擇 [/name]

1. 在一般命令提示字元視窗中，執行下列命令：

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

1. 在 Gremlin shell 中執行下列命令，以連接到 Gremlin 端點：

   ```bash
   :remote connect tinkerpop.server conf/remote-localcompute.yaml
   :remote console
   :> g.V()
   :> g.addV('person1').property(id, '1').property('name', 'somename1')
   :> g.addV('person2').property(id, '2').property('name', 'somename2')
   :> g.V()
   ```

## <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>將本機模擬器解除安裝

使用下列步驟來卸載模擬器：

1. 結束本機模擬器的所有開啟的實例，方法是以滑鼠右鍵按一下系統匣上的**Azure Cosmos 模擬器**圖示，然後選取 [結束 **]。** 結束所有執行個體可能需要數分鐘的時間。

1. 在 [Windows 搜尋] 方塊中，輸入 **應用程式 & 功能** ，並 ** (系統設定) 結果中選取應用程式 & 功能 ** 。

1. 在應用程式清單中，依序選取 [ **Azure Cosmos DB 模擬器**]、[卸載]，然後按一下 [ **卸載**]，然後再次選取 [ **卸載** ]。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何使用本機模擬器來進行免費的本機開發。 您現在可以繼續進行下一篇文章：

* [匯出 Azure Cosmos 模擬器憑證以與 JAVA、Python 和 Node.js apps 搭配使用](local-emulator-export-ssl-certificates.md)
* [使用命令列參數和 PowerShell 命令來控制模擬器](emulator-command-line-parameters.md)
* [模擬器的偵錯工具問題](troubleshoot-local-emulator.md)
