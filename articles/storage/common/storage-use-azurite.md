---
title: 使用 Azurite 模擬器進行本機 Azure 儲存體開發
description: Azurite 開放原始碼模擬器（預覽）提供免費的本機環境來測試您的 Azure 儲存體應用程式。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/01/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.openlocfilehash: af846b0c203934468b7f6282234819142093286f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85512149"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development-and-testing-preview"></a>使用 Azurite 模擬器進行本機 Azure 儲存體開發和測試（預覽）

Azurite 版本3.2 開放原始碼模擬器（預覽）提供免費的本機環境來測試您的 Azure blob 和佇列儲存體應用程式。 當您對應用程式在本機運作的方式感到滿意時，請切換至使用雲端中的 Azure 儲存體帳戶。 模擬器提供 Windows、Linux 和 macOS 的跨平臺支援。 Azurite v3 支援 Azure Blob 服務所執行的 Api。

Azurite 是未來的儲存體模擬器平臺。 Azurite 會取代[Azure 儲存體模擬器](storage-use-emulator.md)。 Azurite 將繼續更新，以支援最新版本的 Azure 儲存體 Api。

有幾種不同的方式可在您的本機系統上安裝和執行 Azurite：

  1. [安裝並執行 Azurite Visual Studio Code 擴充功能](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [使用 NPM 安裝及執行 Azurite](#install-and-run-azurite-by-using-npm)
  1. [安裝並執行 Azurite Docker 映射](#install-and-run-the-azurite-docker-image)
  1. [從 GitHub 存放庫複製、建立和執行 Azurite](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>安裝並執行 Azurite Visual Studio Code 擴充功能

在 Visual Studio Code 中，選取 [**擴充**功能] 窗格，然後在 [**擴充功能： MARKETPLACE**] 中搜尋*Azurite* 。

![Visual Studio Code 延伸模組 marketplace](media/storage-use-azurite/azurite-vs-code-extension.png)

您也可以在瀏覽器中流覽至[Visual Studio Code 延伸模組市場](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite)。 選取 [**安裝**] 按鈕以開啟 Visual Studio Code，並直接移至 [Azurite 延伸模組] 頁面。

您可以在 [Visual Studio Code] 狀態列中快速啟動或關閉 Azurite。 按一下 **[Azurite Blob 服務]** 或 **[Azurite 佇列服務]**。

延伸模組支援下列 Visual Studio Code 命令。 若要開啟命令選擇區，請在 Visual Studio Code 中按 F1 鍵。 

   - **Azurite：清除**-重設所有 Azurite 服務持續性資料
   - **Azurite：清除 Blob 服務**-清除 blob 服務
   - **Azurite：清除佇列服務**-清除佇列服務
   - **Azurite： close** -關閉所有 Azurite 服務
   - **Azurite：關閉 Blob 服務**-關閉 blob 服務
   - **Azurite：關閉佇列服務**-關閉佇列服務
   - **Azurite：開始**-啟動所有 Azurite 服務
   - **Azurite：啟動 Blob 服務**-啟動 blob 服務
   - **Azurite：啟動佇列服務**-啟動佇列服務

若要在 Visual Studio Code 內設定 Azurite，請選取 [擴充功能] 窗格。 選取 [ **Azurite**] 的 [**管理**] （齒輪）圖示。 選取 [**延伸模組設定**]。

![Azurites 設定延伸模組設定](media/storage-use-azurite/azurite-configure-extension-settings.png)

支援下列設定：

   - **Azurite： Blob 主機**-Blob 服務接聽端點。 預設設定為127.0.0.1。
   - **Azurite： Blob 埠**-Blob 服務接聽埠。 預設通訊埠為10000。
   - **Azurite： Cert** -本機信任的 PEM 或 PFX 憑證檔案路徑的路徑，以啟用 HTTPS 模式。
   - **Azurite： debug** -將 Debug 記錄輸出至 Azurite 通道。 預設值為 **false**。
   - **Azurite：** 在本機受信任的 PEM 金鑰檔案的索引鍵路徑， **Azurite： Cert**指向 PEM 檔案時需要此檔案。
   - **Azurite： location** -工作區位置路徑。 預設為 Visual Studio Code 工作資料夾。
   - **Azurite：鬆散**啟用鬆散模式，這會忽略不支援的標頭和參數。
   - **Azurite： oauth** -選擇性 oauth 層級。
   - **Azurite： Pwd** -PFX 檔案的密碼。 當**Azurite： Cert**指向 PFX 檔案時為必要。
   - **Azurite： Queue Host** -佇列服務接聽端點。 預設設定為127.0.0.1。
   - **Azurite：佇列埠**-佇列服務接聽埠。 預設通訊埠為10001。
   - **Azurite：無**訊息-無訊息模式會停用存取記錄。 預設值為 **false**。

## <a name="install-and-run-azurite-by-using-npm"></a>使用 NPM 安裝及執行 Azurite

此安裝方法需要安裝[Node.js 8.0 版或更新版本](https://nodejs.org)。 節點套件管理員（npm）是每個 Node.js 安裝所包含的封裝管理工具。 安裝 Node.js 之後，請執行下列 `npm` 命令來安裝 Azurite。

```console
npm install -g azurite
```

安裝 Azurite 之後，請參閱[從命令列執行 Azurite](#run-azurite-from-a-command-line)。

## <a name="install-and-run-the-azurite-docker-image"></a>安裝並執行 Azurite Docker 映射

使用[DockerHub](https://hub.docker.com/)來提取[最新的 Azurite 映射](https://hub.docker.com/_/microsoft-azure-storage-azurite)，方法是使用下列命令：

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**執行 Azurite Docker 映射**：

下列命令會執行 Azurite Docker 映射。 參數會將 `-p 10000:10000` 要求從主機電腦的埠10000重新導向至 Docker 實例。

```console
docker run -p 10000:10000 -p 10001:10001 \
    mcr.microsoft.com/azure-storage/azurite
```

**指定工作區位置**：

在下列範例中， `-v c:/azurite:/data` 參數會將*c：/azurite*指定為 azurite 保存的資料位置。 在執行 Docker 命令之前，必須先建立目錄（ *c：/azurite*）。

```console
docker run -p 10000:10000 -p 10001:10001 \
    -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**僅執行 blob 服務**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite \
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

如需在啟動時設定 Azurite 的詳細資訊，請參閱[命令列選項](#command-line-options)。

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>從 GitHub 存放庫複製、建立和執行 Azurite

此安裝方法會要求您必須安裝[Git](https://git-scm.com/) 。 使用下列主控台命令來複製 Azurite 專案的[GitHub 存放庫](https://github.com/azure/azurite)。

```console
git clone https://github.com/Azure/Azurite.git
```

複製原始程式碼之後，請從複製之存放庫的根目錄執行下列命令，以建立和安裝 Azurite。

```console
npm install
npm run build
npm install -g
```

安裝和建立 Azurite 之後，請參閱[從命令列執行 Azurite](#run-azurite-from-a-command-line)。

## <a name="run-azurite-from-a-command-line"></a>從命令列執行 Azurite

> [!NOTE]
> 如果您只安裝了 Visual Studio Code 延伸模組，則無法從命令列執行 Azurite。 請改用 Visual Studio Code 命令選擇區。 如需詳細資訊，請參閱[安裝並執行 Azurite Visual Studio Code 延伸](#install-and-run-the-azurite-visual-studio-code-extension)模組。

若要立即開始使用命令列，請建立名為*c:\azurite*的目錄，然後發出下列命令來啟動 azurite：

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

此命令會告訴 Azurite 將所有資料儲存在特定目錄*c:\azurite*中。 如果 `--location` 省略選項，則會使用目前的工作目錄。

## <a name="command-line-options"></a>命令列選項

本節詳細說明啟動 Azurite 時可用的命令列參數。

### <a name="help"></a>[說明]

**選擇性**-使用或參數取得命令列說明 `-h` `--help` 。

```console
azurite -h
azurite --help
```

### <a name="blob-listening-host"></a>Blob 接聽主機

**選擇性**-根據預設，Azurite 會接聽127.0.0.1 做為本機伺服器。 使用 `--blobHost` 參數來設定您的需求的位址。

僅接受本機電腦上的要求：

```console
azurite --blobHost 127.0.0.1
```

允許遠端要求：

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> 允許遠端要求可能會讓您的系統容易遭受外部攻擊。

### <a name="blob-listening-port-configuration"></a>Blob 接聽埠設定

**選擇性**-根據預設，Azurite 會接聽埠10000上的 Blob 服務。 使用 `--blobPort` 參數來指定您所需的接聽埠。

> [!NOTE]
> 使用自訂的埠之後，您必須在 Azure 儲存體工具或 Sdk 中更新連接字串或對應的設定。

自訂 Blob 服務接聽埠：

```console
azurite --blobPort 8888
```

讓系統自動選取可用的埠：

```console
azurite --blobPort 0
```

使用中的埠會在 Azurite 啟動期間顯示。

### <a name="queue-listening-host"></a>佇列接聽主機

**選擇性**-根據預設，Azurite 會接聽127.0.0.1 做為本機伺服器。 使用 `--queueHost` 參數來設定您的需求的位址。

僅接受本機電腦上的要求：

```console
azurite --queueHost 127.0.0.1
```

允許遠端要求：

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> 允許遠端要求可能會讓您的系統容易遭受外部攻擊。

### <a name="queue-listening-port-configuration"></a>佇列接聽埠設定

**選擇性**-根據預設，Azurite 會接聽埠10001上的佇列服務。 使用 `--queuePort` 參數來指定您所需的接聽埠。

> [!NOTE]
> 使用自訂的埠之後，您必須在 Azure 儲存體工具或 Sdk 中更新連接字串或對應的設定。

自訂佇列服務接聽埠：

```console
azurite --queuePort 8888
```

讓系統自動選取可用的埠：

```console
azurite --queuePort 0
```

使用中的埠會在 Azurite 啟動期間顯示。

### <a name="workspace-path"></a>工作區路徑

**選擇性**-Azurite 會在執行期間將資料儲存到本機磁片。 使用 `-l` 或 `--location` 參數，將路徑指定為工作區位置。 根據預設，將會使用目前的處理常式工作目錄。 請注意小寫的 ' l '。

```console
azurite -l c:\azurite
azurite --location c:\azurite
```

### <a name="access-log"></a>存取記錄檔

**選擇性**-根據預設，存取記錄會顯示在主控台視窗中。 使用或參數，停用存取記錄的顯示 `-s` `--silent` 。

```console
azurite -s
azurite --silent
```
### <a name="debug-log"></a>Debug 記錄檔

**選擇性**-debug 記錄檔包含每個要求和例外狀況堆疊追蹤的詳細資訊。 提供或參數的有效本機檔案路徑，以啟用 debug 記錄 `-d` 檔 `--debug` 。

```console
azurite -d path/debug.log
azurite --debug path/debug.log
```

### <a name="loose-mode"></a>鬆散模式

**選擇性**-根據預設，Azurite 會套用 strict 模式來封鎖不支援的要求標頭和參數。 使用或參數停用 strict `-L` 模式 `--loose` 。 請注意大寫的 ' L '。

```console
azurite -L
azurite --loose
```
### <a name="version"></a>版本

**選擇性**-使用或參數顯示已安裝的 Azurite 版本 `-v` 號碼 `--version` 。

```console
azurite -v
azurite --version
```

### <a name="certificate-configuration-https"></a>憑證設定（HTTPS）

**選擇性**-根據預設，Azurite 會使用 HTTP 通訊協定。 提供隱私權增強郵件（pem）或[個人資訊交換（.pfx）](https://docs.microsoft.com/windows-hardware/drivers/install/personal-information-exchange---pfx--files)憑證檔案的路徑給交換器，以啟用 HTTPS 模式 `--cert` 。

當 `--cert` 提供 PEM 檔案的時，您必須提供對應的 `--key` 交換器。

```console
azurite --cert path/server.pem --key path/key.pem
```

當 `--cert` 提供 PFX 檔案的時，您必須提供對應的 `--pwd` 交換器。

```console
azurite --cert path/server.pfx --pwd pfxpassword
```

如需建立 PEM 和 PFX 檔案的詳細資訊，請參閱[HTTPS 設定](https://github.com/Azure/Azurite/blob/master/README.md#https-setup)。

### <a name="oauth-configuration"></a>OAuth 設定

**選擇性**-使用參數啟用 Azurite 的 OAuth 驗證 `--oauth` 。

```console
azurite --oauth basic --cert path/server.pem --key path/key.pem
```

> [!NOTE]
> OAuth 需要 HTTPS 端點。 請確定已透過提供 `--cert` 交換器和交換器來啟用 HTTPS `--oauth` 。

Azurite 會藉由指定參數來支援基本驗證 `basic` `--oauth` 。 Azurite 會執行基本驗證，例如驗證傳入的持有人權杖、檢查簽發者、物件和到期日。 Azurite 不會檢查權杖簽章或許可權。

## <a name="authorization-for-tools-and-sdks"></a>工具和 Sdk 的授權

使用任何驗證策略，從 Azure 儲存體 Sdk 或工具（例如[Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)）連接到 Azurite。 需要驗證。 Azurite 支援使用 OAuth、共用金鑰和共用存取簽章（SAS）進行授權。 Azurite 也支援公用容器的匿名存取。

如果您使用的是 Azure Sdk，請使用選項開始 Azurite `--oauth basic and --cert --key/--pwd` 。

### <a name="well-known-storage-account-and-key"></a>知名的儲存體帳戶和金鑰

Azurite 會接受舊版 Azure 儲存體模擬器所使用的相同知名帳戶和金鑰。

- 帳戶名稱：`devstoreaccount1`
- 帳戶金鑰：`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

### <a name="custom-storage-accounts-and-keys"></a>自訂儲存體帳戶和金鑰

Azurite 支援自訂儲存體帳戶名稱和金鑰，方法是 `AZURITE_ACCOUNTS` 以下列格式設定環境變數： `account1:key1[:key2];account2:key1[:key2];...` 。

例如，使用具有一個金鑰的自訂儲存體帳戶：

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

```bash
export AZURITE_ACCOUNTS="account1:key1"
```

或使用具有兩個金鑰的多個儲存體帳戶：

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

```bash
export AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

根據預設，Azurite 會每分鐘重新整理來自環境變數的自訂帳戶名稱和金鑰。 透過這項功能，您可以在不重新開機 Azurite 的情況下，動態地旋轉帳戶金鑰，或新增新的儲存體帳戶。

> [!NOTE]
> `devstoreaccount1`當您設定自訂儲存體帳戶時，預設儲存體帳戶會停用。

### <a name="connection-strings"></a>連接字串

從您的應用程式連線到 Azurite 最簡單的方式，就是在應用程式的設定檔中設定參考快捷方式*UseDevelopmentStorage = true*的連接字串。 以下是*app.config*檔案中的連接字串範例：

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="http-connection-strings"></a>HTTP 連接字串

您可以將下列連接字串傳遞至[Azure sdk](https://aka.ms/azsdk)或工具，例如 Azure CLI 2.0 或儲存體總管。

完整的連接字串為：

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

若只要連接到 blob 服務，連接字串為：

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;`

若只要連接到佇列服務，連接字串為：

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

#### <a name="https-connection-strings"></a>HTTPS 連接字串

完整的 HTTPS 連接字串為：

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

若只要使用 blob 服務，HTTPS 連接字串為：

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;`

若只要使用佇列服務，HTTPS 連接字串為：

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

如果您使用 `dotnet dev-certs` 來產生自我簽署憑證，請使用下列連接字串。

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://localhost:10000/devstoreaccount1;QueueEndpoint=https://localhost:10001/devstoreaccount1;`

使用[自訂儲存體帳戶和金鑰](#custom-storage-accounts-and-keys)時，請更新連接字串。

如需詳細資訊，請參閱[設定 Azure 儲存體連接字串](storage-configure-connection-string.md)。

### <a name="azure-sdks"></a>Azure SDK

若要搭配使用 Azurite 與[Azure sdk](https://aka.ms/azsdk)，請使用 OAUTH 和 HTTPS 選項：

```console
azurite --oauth basic --cert certname.pem --key certname-key.pem
```

#### <a name="azure-blob-storage"></a>Azure Blob 儲存體

然後，您可以將 BlobContainerClient、BlobServiceClient 或 BlobClient 具現化。

```csharp
// With container URL and DefaultAzureCredential
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new BlobContainerClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "container-name"
  );

// With account name and key
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

#### <a name="azure-queue-storage"></a>Azure 佇列儲存體

您也可以具現化 QueueClient 或 QueueServiceClient。

```csharp
// With queue URL and DefaultAzureCredential
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new QueueClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "queue-name"
  );

// With account name and key
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure 儲存體總管

您可以使用儲存體總管來查看儲存在 Azurite 中的資料。

#### <a name="connect-to-azurite-using-http"></a>使用 HTTP 連接到 Azurite

在儲存體總管中，遵循下列步驟來連接到 Azurite：

 1. 選取 [**管理帳戶**] 圖示
 1. 選取 [**新增帳戶**]
 1. 選取 [**附加至本機模擬器**]
 1. 選取 [**下一步**]
 1. 將 [**顯示名稱**] 欄位編輯為您選擇的名稱
 1. 再次選取 **[下一步]**
 1. 選取 **[連線]**

#### <a name="connect-to-azurite-using-https"></a>使用 HTTPS 連接到 Azurite

根據預設，儲存體總管不會開啟使用自我簽署憑證的 HTTPS 端點。 如果您是使用 HTTPS 執行 Azurite，您可能會使用自我簽署憑證。 在儲存體總管中，透過 [**編輯**  ->  **ssl 憑證**] [匯  ->  **入憑證**] 對話方塊來匯入 ssl 憑證。

##### <a name="import-certificate-to-storage-explorer"></a>將憑證匯入儲存體總管

1. 在您的本機電腦上尋找憑證。
1. 在儲存體總管中，移至 [**編輯**  ->  **SSL 憑證**] [匯  ->  **入憑證**] 並匯入您的憑證。

如果您未匯入憑證，將會收到錯誤：

`unable to verify the first certificate` 或 `self signed certificate in chain`

##### <a name="add-azurite-via-https-connection-string"></a>透過 HTTPS 連接字串新增 Azurite

請遵循下列步驟，將 Azurite HTTPS 新增至儲存體總管：

1. 選取**切換瀏覽器**
1. 選取 [**本機 & 附加**]
1. 以滑鼠右鍵按一下 [**儲存體帳戶**]，然後選取 **[連線到 Azure 儲存體]**。
1. 選取 [**使用連接字串**]
1. 選取 [下一步]。
1. 在 [**顯示名稱**] 欄位中輸入值。
1. 輸入本檔上一節的[HTTPS 連接字串](#https-connection-strings)
1. 選取 [**下一步**]
1. 選取 **[連線]**

## <a name="workspace-structure"></a>工作區結構

初始化 Azurite 時，可以在工作區位置中建立下列檔案和資料夾。

- `__blobstorage__`-包含 Azurite blob 服務保存二進位資料的目錄
- `__queuestorage__`-包含 Azurite 佇列服務保存二進位資料的目錄
- `__azurite_db_blob__.json`-Azurite blob 服務中繼資料檔案
- `__azurite_db_blob_extent__.json`-Azurite blob 服務範圍中繼資料檔案
- `__azurite_db_queue__.json`-Azurite 佇列服務中繼資料檔案
- `__azurite_db_queue_extent__.json`-Azurite 佇列服務範圍中繼資料檔案

若要清除 Azurite，請刪除上述檔案和資料夾，然後重新開機模擬器。

## <a name="differences-between-azurite-and-azure-storage"></a>Azurite 和 Azure 儲存體之間的差異

Azurite 的本機實例與雲端中的 Azure 儲存體帳戶之間有功能上的差異。

### <a name="endpoint-and-connection-url"></a>端點和連線 URL

Azurite 的服務端點不同于 Azure 儲存體帳戶的端點。 本機電腦不會執行功能變數名稱解析，要求 Azurite 端點必須是本機位址。

當您在 Azure 儲存體帳戶中定址資源時，帳戶名稱會是 URI 主機名稱的一部分。 所要定址的資源是 URI 路徑的一部分：

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

下列 URI 是 Azure 儲存體帳戶中 blob 的有效位址：

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

因為本機電腦不會執行功能變數名稱解析，所以帳戶名稱是 URI 路徑的一部分，而不是主機名稱。 針對 Azurite 中的資源，請使用下列 URI 格式：

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

下列位址可能用於存取 Azurite 中的 blob：

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>調整和效能

Azurite 不支援大量已連線的用戶端。 沒有任何效能保證。 Azurite 適用于開發和測試用途。

### <a name="error-handling"></a>錯誤處理

Azurite 與 Azure 儲存體錯誤處理邏輯一致，但有一些差異。 例如，錯誤訊息可能會不同，而錯誤狀態碼則會對齊。

### <a name="ra-grs"></a>RA-GRS

Azurite 支援讀取權限異地冗余複寫（RA-GRS）。 針對儲存體資源，請將附加至帳戶名稱來存取次要位置 `-secondary` 。 例如，下列位址可能會用於在 Azurite 中使用唯讀次要資料庫來存取 blob：

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite 是開放原始碼

歡迎 Azurite 的貢獻和建議。 請移至 Azurite [GitHub 專案](https://github.com/Azure/Azurite/projects)頁面或[github 問題](https://github.com/Azure/Azurite/issues)，以取得我們正在追蹤的後續功能和 bug 修正的里程碑和工作專案。 GitHub 中也會追蹤詳細的工作專案。

## <a name="next-steps"></a>後續步驟

- [使用 azure 儲存體模擬器進行開發和測試](storage-use-emulator.md)檔舊版 azure 儲存體模擬器，其已由 Azurite 所取代。
- [設定 Azure 儲存體連接字串](storage-configure-connection-string.md)說明如何組合有效的 Azure 儲存體連接字串。
