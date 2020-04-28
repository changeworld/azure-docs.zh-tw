---
title: 使用 Azurite 模擬器進行本機 Azure 儲存體開發
description: Azurite 開放原始碼模擬器（預覽）提供免費的本機環境來測試您的 Azure 儲存體應用程式。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 5e1fce0852a4e820d7ee0af626ce3fddf6773750
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76029918"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development-and-testing-preview"></a>使用 Azurite 模擬器進行本機 Azure 儲存體開發和測試（預覽）

Azurite 版本3.2 開放原始碼模擬器（預覽）提供免費的本機環境來測試您的 Azure blob 和佇列儲存體應用程式。 當您對應用程式在本機運作的方式感到滿意時，請切換至使用雲端中的 Azure 儲存體帳戶。 模擬器提供 Windows、Linux 和 MacOS 的跨平臺支援。 Azurite v3 支援 Azure Blob 服務所執行的 Api。

Azurite 是未來的儲存體模擬器平臺。 Azurite 會取代[Azure 儲存體模擬器](storage-use-emulator.md)。 Azurite 將繼續更新，以支援最新版本的 Azure 儲存體 Api。

有幾種不同的方式可在您的本機系統上安裝和執行 Azurite：

  1. [安裝並執行 Azurite Visual Studio Code 擴充功能](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [使用 NPM 安裝及執行 Azurite](#install-and-run-azurite-by-using-npm)
  1. [安裝並執行 Azurite Docker 映射](#install-and-run-the-azurite-docker-image)
  1. [從 GitHub 存放庫複製、建立和執行 Azurite](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>安裝並執行 Azurite Visual Studio Code 擴充功能

在 Visual Studio Code 中，選取 [**擴充**功能] 窗格，然後在 [**擴充功能： MARKETPLACE**] 中搜尋*Azurite* 。

![Visual Studio Code 延伸模組 marketplace](media/storage-use-azurite/azurite-vs-code-extension.png)

或者，在您的瀏覽器中流覽至[VS Code 延伸模組市場](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite)。 選取 [**安裝**] 按鈕以開啟 Visual Studio Code，並直接移至 [Azurite 延伸模組] 頁面。

您可以快速啟動或關閉 Azurite，方法是按一下 VS Code 狀態列中的 **[Azurite Blob 服務]** 或 **[Azurite 佇列服務]** ，或在 VS Code 命令選擇區中發出下列命令。 若要開啟命令選擇區，請在 VS Code 中按**F1**鍵。

延伸模組支援下列 Visual Studio Code 命令：

   * **Azurite：開始**-啟動所有 Azurite 服務
   * **Azurite： close** -關閉所有 Azurite 服務
   * **Azurite：清除**-重設所有 Azurite 服務持續性資料
   * **Azurite：啟動 Blob 服務**-啟動 blob 服務
   * **Azurite：關閉 Blob 服務**-關閉 blob 服務
   * **Azurite：清除 Blob 服務**-清除 blob 服務
   * **Azurite：啟動佇列服務**-啟動佇列服務
   * **Azurite：關閉佇列服務**-關閉佇列服務
   * **Azurite：清除佇列服務**-清除佇列服務

若要在 Visual Studio Code 內設定 Azurite，請選取 [擴充功能] 窗格。 選取 [ **Azurite**] 的 [**管理**] （齒輪）圖示。 選取 [**設定延伸模組設定**]。

![Azurite 設定延伸模組設定](media/storage-use-azurite/azurite-configure-extension-settings.png)

支援下列設定：

   * **Azurite： Blob 主機**-Blob 服務接聽端點。 預設設定為127.0.0.1。
   * **Azurite： Blob 埠**-Blob 服務接聽埠。 預設通訊埠為10000。
   * **Azurite： debug** -將 Debug 記錄輸出至 Azurite 通道。 預設值為 **false**。
   * **Azurite： location** -工作區位置路徑。 預設為 Visual Studio Code 工作資料夾。
   * **Azurite： Queue Host** -佇列服務接聽端點。 預設設定為127.0.0.1。
   * **Azurite：佇列埠**-佇列服務接聽埠。 預設通訊埠為10001。
   * **Azurite：無**訊息-無訊息模式會停用存取記錄。 預設值為 **false**。

## <a name="install-and-run-azurite-by-using-npm"></a>使用 NPM 安裝及執行 Azurite

此安裝方法會要求您必須安裝[node.js 8.0 版或更新](https://nodejs.org)版本。 **npm**是每個 node.js 安裝內含的套件管理工具。 安裝 node.js 之後，請執行下列**npm**命令來安裝 Azurite。

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

下列命令會執行 Azurite Docker 映射。 參數`-p 10000:10000`會將要求從主機電腦的埠10000重新導向至 Docker 實例。

```console
docker run -p 10000:10000 -p 10001:10001 mcr.microsoft.com/azure-storage/azurite
```

**指定工作區位置**：

在下列範例中， `-v c:/azurite:/data`參數會將*c：/Azurite*指定為 azurite 保存的資料位置。 在執行 Docker 命令之前，必須先建立目錄（ *c：/azurite*）。

```console
docker run -p 10000:10000 -p 10001:10001 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**僅執行 blob 服務**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

**設定所有 Azurite 參數**：

這個範例示範如何設定所有的命令列參數。 下列所有參數都應該放在單一命令列上。

```console
docker run -p 8888:8888
           -p 9999:9999
           -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite
           -l /workspace
           -d /workspace/debug.log
           --blobPort 8888
           --blobHost 0.0.0.0
           --queuePort 9999
           --queueHost 0.0.0.0
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
> 如果您只安裝了 Visual Studio Code 延伸模組，則無法從命令列執行 Azurite。 請改用 VS Code 命令選擇區。 如需詳細資訊，請參閱[安裝並執行 Azurite Visual Studio Code 延伸](#install-and-run-the-azurite-visual-studio-code-extension)模組。

若要立即開始使用命令列，請建立名為**c:\azurite**的目錄，然後發出下列命令來啟動 azurite：

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

此命令會告訴 Azurite 將所有資料儲存在特定目錄**c:\azurite**中。 如果省略 **--location**選項，則會使用目前的工作目錄。

## <a name="command-line-options"></a>命令列選項

本節詳細說明啟動 Azurite 時可用的命令列參數。 所有命令列參數都是選擇性的。

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>] 
    [-d | --debug <log file path>] [-l | --location <workspace path>]
    [--queueHost <IP address>] [--queuePort <port address>]
    [-s | --silent] [-h | --help]
```

**-D**是 **--debug**的快捷方式， **-l**參數是-- **location**的快捷方式，- **s**是 **--silent**的快捷方式，而 **-h**是 **--help**的快捷方式。

### <a name="blob-listening-host"></a>Blob 接聽主機

**選擇性**根據預設，Azurite 會接聽127.0.0.1 做為本機伺服器。 使用 **--blobHost**參數來設定您的需求的位址。

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

**選擇性**根據預設，Azurite 會接聽埠10000上的 Blob 服務。 使用 **--blobPort**參數來指定您所需的接聽埠。

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

**選擇性**根據預設，Azurite 會接聽127.0.0.1 做為本機伺服器。 使用 **--queueHost**參數來設定您的需求的位址。

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

**選擇性**根據預設，Azurite 會接聽埠10001上的佇列服務。 使用 **--queuePort**參數來指定您所需的接聽埠。

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

**選擇性**Azurite 會在執行期間將資料儲存到本機磁片。 使用 **--location**參數將路徑指定為工作區位置。 根據預設，將會使用目前的處理常式工作目錄。

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>存取記錄檔

**選擇性**根據預設，存取記錄會顯示在主控台視窗中。 使用 **--silent**參數來停用存取記錄檔的顯示。

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>Debug 記錄檔

**選擇性**Debug 記錄檔包含每個要求和例外狀況堆疊追蹤的詳細資訊。 藉由提供有效的本機檔案路徑給 **--debug**參數，啟用 debug 記錄檔。

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

### <a name="loose-mode"></a>鬆散模式

**選擇性**根據預設，Azurite 會套用 strict 模式來封鎖不支援的要求標頭和參數。 使用 **--鬆散**參數停用 strict 模式。

```console
azurite --loose
```

請注意大寫的 ' L ' 快捷方式參數：

```console
azurite -L
```

## <a name="authorization-for-tools-and-sdks"></a>工具和 Sdk 的授權

使用任何驗證策略，從 Azure 儲存體 Sdk 或工具（例如[Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)）連接到 Azurite。 需要驗證。 Azurite 支援使用共用金鑰和共用存取簽章（SAS）進行授權。 Azurite 也支援公用容器的匿名存取。

### <a name="well-known-storage-account-and-key"></a>知名的儲存體帳戶和金鑰

您可以使用下列帳戶名稱和金鑰搭配 Azurite。 這是舊版 Azure 儲存體模擬器所使用的相同知名帳戶和金鑰。

* 帳戶名稱：`devstoreaccount1`
* 帳戶金鑰：`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> 除了 SharedKey authentication，Azurite 也支援帳戶和服務 SAS 驗證。 當容器設定為允許公用存取時，也可以使用匿名存取。

### <a name="connection-string"></a>連接字串

從您的應用程式連線到 Azurite 最簡單的方式，就是在應用程式的設定檔中設定參考快捷方式*UseDevelopmentStorage = true*的連接字串。 以下是*app.config*檔案中的連接字串範例：

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

如需詳細資訊，請參閱[設定 Azure 儲存體連接字串](storage-configure-connection-string.md)。

### <a name="custom-storage-accounts-and-keys"></a>自訂儲存體帳戶和金鑰

Azurite 支援自訂儲存體帳戶名稱和金鑰，方法`AZURITE_ACCOUNTS`是以下列格式設定環境變數`account1:key1[:key2];account2:key1[:key2];...`：。

例如，使用具有一個金鑰的自訂儲存體帳戶：

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

或使用多個具有2個金鑰的儲存體帳戶：

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

根據預設，Azurite 會每分鐘重新整理來自環境變數的自訂帳戶名稱和金鑰。 透過這項功能，您可以在不重新開機 Azurite 的情況下，動態地旋轉帳戶金鑰，或新增新的儲存體帳戶。

> [!NOTE]
> 當您`devstoreaccount1`設定自訂儲存體帳戶時，預設儲存體帳戶會停用。

> [!NOTE]
> 使用自訂帳戶名稱和金鑰時，請據以更新連接字串。

> [!NOTE]
> 使用`export`關鍵字在 Linux 環境中設定環境變數，並在 Windows `set`中使用。

### <a name="storage-explorer"></a>儲存體總管

在 Azure 儲存體總管中，按一下 [**新增帳戶**] 圖示以連線至 Azurite，然後選取 [**附加至本機模擬器**]，再按一下 **[連接**]。

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

Azurite 不是可調整的儲存體服務，且不支援大量的並行用戶端。 沒有任何效能保證。 Azurite 適用于開發和測試用途。

### <a name="error-handling"></a>錯誤處理

Azurite 與 Azure 儲存體錯誤處理邏輯一致，但有一些差異。 例如，錯誤訊息可能會不同，而錯誤狀態碼則會對齊。

### <a name="ra-grs"></a>RA-GRS

Azurite 支援讀取權限異地冗余複寫（RA-GRS）。 針對儲存體資源，請將 **-次要**附加至帳戶名稱，以存取次要位置。 例如，下列位址可能會用於在 Azurite 中使用唯讀次要資料庫來存取 blob：

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite 是開放原始碼

歡迎 Azurite 的貢獻和建議。 請移至 Azurite [GitHub 專案](https://github.com/Azure/Azurite/projects)頁面或[github 問題](https://github.com/Azure/Azurite/issues)，以取得我們正在追蹤的後續功能和 bug 修正的里程碑和工作專案。 GitHub 中也會追蹤詳細的工作專案。

## <a name="next-steps"></a>後續步驟

* [使用 azure 儲存體模擬器進行開發和測試](storage-use-emulator.md)檔舊版 azure 儲存體模擬器，其已由 Azurite 所取代。
* [設定 Azure 儲存體連接字串](storage-configure-connection-string.md)說明如何組合有效的 Azure 儲存體連接字串。
