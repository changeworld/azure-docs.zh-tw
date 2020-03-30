---
title: 使用 Azurite 模擬器進行本地 Azure 存儲開發
description: Azurite 開源模擬器（預覽版）為測試 Azure 存儲應用程式提供了一個免費的本地環境。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 5e1fce0852a4e820d7ee0af626ce3fddf6773750
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76029918"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development-and-testing-preview"></a>使用 Azurite 模擬器進行本地 Azure 存儲開發和測試（預覽）

Azurite 版本 3.2 開源模擬器（預覽版）為測試 Azure Blob 和佇列存儲應用程式提供了一個免費的本地環境。 當您對應用程式在本地的工作方式感到滿意時，切換到在雲中使用 Azure 存儲帳戶。 模擬程式在 Windows、Linux 和 MacOS 上提供跨平臺支援。 Azurite v3 支援 Azure Blob 服務實現的 API。

Azurite 是未來的存儲模擬器平臺。 Azurite 取代 Azure[存儲模擬器](storage-use-emulator.md)。 Azurite 將繼續更新以支援最新版本的 Azure 存儲 API。

在本地系統上安裝和運行 Azurite 有幾種不同的方法：

  1. [安裝並運行 Azurite 視覺化工作室代碼擴展](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [使用 NPM 安裝和運行 Azurite](#install-and-run-azurite-by-using-npm)
  1. [安裝並運行 Azurite Docker 映射](#install-and-run-the-azurite-docker-image)
  1. [從 GitHub 存儲庫克隆、生成和運行 Azurite](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>安裝並運行 Azurite 視覺化工作室代碼擴展

在視覺化工作室代碼中，選擇 **"擴展"** 窗格，並在 **"擴展：市場**"中搜索*Azurite。*

![視覺化工作室代碼擴展市場](media/storage-use-azurite/azurite-vs-code-extension.png)

或者，在瀏覽器中導航到[VS 代碼擴展市場](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite)。 選擇 **"安裝**"按鈕以打開視覺化工作室代碼，然後直接轉到 Azurite 擴展頁。

您可以通過按一下 VS Code 狀態列中的 **[Azurite Blob 服務]** 或 **[Azurite 佇列服務]** 或在 VS Code 命令調色板中發出以下命令來快速啟動或關閉 Azurite。 要打開命令調色板，請按 VS 代碼中的**F1。**

擴展支援以下視覺化工作室代碼命令：

   * **阿祖瑞特：開始**- 開始所有阿祖瑞特服務
   * **阿祖瑞特：關閉**- 關閉所有阿祖雷特服務
   * **Azurite： 清潔**- 重置所有 Azurite 服務持久性資料
   * **Azurite： 啟動 Blob 服務**- 啟動 Blob 服務
   * **Azurite： 關閉 Blob 服務**- 關閉 Blob 服務
   * **Azurite： 清潔 Blob 服務**- 清潔 Blob 服務
   * **Azurite： 啟動佇列服務**- 啟動佇列服務
   * **Azurite： 關閉佇列服務**- 關閉佇列服務
   * **Azurite： 清潔佇列服務**- 清潔佇列服務

要在視覺化工作室代碼中配置 Azurite，請選擇擴展窗格。 選擇**Azurite**的 **"管理**（齒輪）"圖示。 選擇 **"配置擴展設置**"。

![Azurite 配置擴展設置](media/storage-use-azurite/azurite-configure-extension-settings.png)

支援以下設置：

   * **Azurite： Blob 主機**- Blob 服務偵聽終結點。 預設設置為 127.0.0.1。
   * **Azurite： Blob 埠**- Blob 服務偵聽埠。 預設埠為 10000。
   * **Azurite：調試**- 將調試日誌輸出到 Azurite 通道。 預設值為 **false**。
   * **Azurite：位置**- 工作區位置路徑。 預設值為"視覺化工作室代碼"工作資料夾。
   * **Azurite： 佇列主機**- 佇列服務偵聽終結點。 預設設置為 127.0.0.1。
   * **Azurite： 佇列埠**- 佇列服務偵聽埠。 預設埠為 10001。
   * **Azurite： 靜默**- 靜音模式禁用訪問日誌。 預設值為 **false**。

## <a name="install-and-run-azurite-by-using-npm"></a>使用 NPM 安裝和運行 Azurite

此安裝方法要求您安裝[Node.js 版本 8.0 或更高版本](https://nodejs.org)。 **npm**是每個 Node.js 安裝中包含的包管理工具。 安裝 Node.js 後，執行以下**npm**命令以安裝 Azurite。

```console
npm install -g azurite
```

安裝 Azurite 後，請參閱[從命令列運行 Azurite。](#run-azurite-from-a-command-line)

## <a name="install-and-run-the-azurite-docker-image"></a>安裝並運行 Azurite Docker 映射

使用[DockerHub](https://hub.docker.com/)使用以下命令提取[最新的 Azurite 映射](https://hub.docker.com/_/microsoft-azure-storage-azurite)：

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**運行 Azurite Docker 映射**：

以下命令運行 Azurite Docker 映射。 參數`-p 10000:10000`將請求從主機的埠 10000 重定向到 Docker 實例。

```console
docker run -p 10000:10000 -p 10001:10001 mcr.microsoft.com/azure-storage/azurite
```

**指定工作區位置**：

在下面的示例中，`-v c:/azurite:/data`參數指定*c：/azurite*作為 Azurite 持久化的資料位置。 在運行 Docker 命令之前，必須創建目錄*c：/azurite。*

```console
docker run -p 10000:10000 -p 10001:10001 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**僅運行 blob 服務**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

**設置所有 Azurite 參數**：

此示例演示如何設置所有命令列參數。 以下所有參數都應放在單個命令列上。

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

有關在啟動時配置 Azurite 的詳細資訊，請參閱[命令列選項](#command-line-options)。

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>從 GitHub 存儲庫克隆、生成和運行 Azurite

此安裝方法要求您安裝[Git。](https://git-scm.com/) 使用以下主控台命令克隆 Azurite 專案的[GitHub 存儲庫](https://github.com/azure/azurite)。

```console
git clone https://github.com/Azure/Azurite.git
```

克隆原始程式碼後，從克隆的回購的根部執行以下命令以生成和安裝 Azurite。

```console
npm install
npm run build
npm install -g
```

安裝和生成 Azurite 後，請參閱[從命令列運行 Azurite。](#run-azurite-from-a-command-line)

## <a name="run-azurite-from-a-command-line"></a>從命令列運行 Azurite

> [!NOTE]
> 如果只安裝了 Visual Studio 代碼擴展，則無法從命令列運行 Azurite。 而是使用 VS Code 命令調色板。 有關詳細資訊，請參閱[安裝和運行 Azurite 視覺化工作室代碼擴展](#install-and-run-the-azurite-visual-studio-code-extension)。

要立即開始使用命令列，請創建一個名為**c：_azurite 的**目錄，然後通過發出以下命令啟動 Azurite：

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

此命令告訴 Azurite 將所有資料存儲在特定的目錄中 **，c：\azurite**。 如果省略 **--位置**選項，它將使用當前工作目錄。

## <a name="command-line-options"></a>命令列選項

本節詳細介紹了啟動 Azurite 時可用的命令列開關。 所有命令列交換器都是可選的。

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>] 
    [-d | --debug <log file path>] [-l | --location <workspace path>]
    [--queueHost <IP address>] [--queuePort <port address>]
    [-s | --silent] [-h | --help]
```

**-d**是 **-debug**的快捷方式 **，-l**開關是 **-位置****的快捷方式，-s**是 **-silent**的快捷方式 **，-h**是 **- 説明的**快捷方式。

### <a name="blob-listening-host"></a>Blob 偵聽主機

**可選**預設情況下，Azurite 將偵聽 127.0.0.1 作為本機伺服器。 使用 **--blobHost**開關將位址設置為您的要求。

僅接受本地電腦上的請求：

```console
azurite --blobHost 127.0.0.1
```

允許遠端請求：

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> 允許遠端請求可能會使您的系統容易受到外部攻擊。

### <a name="blob-listening-port-configuration"></a>Blob 偵聽埠配置

**可選**預設情況下，Azurite 將在埠 10000 上偵聽 Blob 服務。 使用 **--blobPort**開關指定所需的偵聽埠。

> [!NOTE]
> 使用自訂埠後，需要在 Azure 存儲工具或 SDK 中更新連接字串或相應的配置。

自訂 Blob 服務偵聽埠：

```console
azurite --blobPort 8888
```

讓系統自動選擇可用埠：

```console
azurite --blobPort 0
```

正在使用的埠顯示在 Azurite 啟動期間。

### <a name="queue-listening-host"></a>佇列偵聽主機

**可選**預設情況下，Azurite 將偵聽 127.0.0.1 作為本機伺服器。 使用 **--queueHost**開關將位址設置為您的要求。

僅接受本地電腦上的請求：

```console
azurite --queueHost 127.0.0.1
```

允許遠端請求：

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> 允許遠端請求可能會使您的系統容易受到外部攻擊。

### <a name="queue-listening-port-configuration"></a>佇列偵聽埠配置

**可選**預設情況下，Azurite 將在埠 10001 上偵聽佇列服務。 使用 **--queuePort**開關指定所需的偵聽埠。

> [!NOTE]
> 使用自訂埠後，需要在 Azure 存儲工具或 SDK 中更新連接字串或相應的配置。

自訂佇列服務偵聽埠：

```console
azurite --queuePort 8888
```

讓系統自動選擇可用埠：

```console
azurite --queuePort 0
```

正在使用的埠顯示在 Azurite 啟動期間。

### <a name="workspace-path"></a>工作區路徑

**可選**Azurite 在執行任務期間將資料存儲到本地磁片。 使用 **--位置**開關將路徑指定為工作區位置。 預設情況下，將使用當前進程工作目錄。

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>存取記錄檔

**可選**預設情況下，訪問日誌顯示在主控台視窗中。 使用 **-- 靜默**開關禁用訪問日誌的顯示。

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>調試日誌

**可選**調試日誌包括有關每個請求和異常堆疊追蹤的詳細資訊。 通過向 **--調試**交換器提供有效的本地檔路徑來啟用調試日誌。

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

### <a name="loose-mode"></a>鬆散模式

**可選**預設情況下，Azurite 應用嚴格模式來阻止不受支援的請求標頭和參數。 使用 **-loose**開關禁用嚴格模式。

```console
azurite --loose
```

請注意大寫"L"快捷方式開關：

```console
azurite -L
```

## <a name="authorization-for-tools-and-sdks"></a>工具和 SDK 的授權

使用任何身份驗證策略從 Azure 存儲 SDK 或工具（如[Azure 存儲資源管理器](https://azure.microsoft.com/features/storage-explorer/)）連接到 Azurite。 身份驗證是必需的。 Azurite 支援使用共用金鑰和共用訪問簽名 （SAS） 進行授權。 Azurite 還支援匿名存取公共容器。

### <a name="well-known-storage-account-and-key"></a>知名存儲帳戶和金鑰

您可以將以下帳戶名稱和金鑰與 Azurite 一起使用。 這是舊版 Azure 存儲模擬器使用的已知帳戶和金鑰。

* 帳戶名稱：`devstoreaccount1`
* 帳戶金鑰：`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> 除了共用金鑰身份驗證外，Azurite 還支援帳戶和服務 SAS 身份驗證。 當容器設置為允許公共訪問時，匿名存取也可用。

### <a name="connection-string"></a>連接字串

從應用程式連接到 Azurite 的最簡單方法是在應用程式的設定檔中配置一個連接字串，該字串引用快捷方式*UseDevelopmentStorage_true*。 下面是*app.config*檔中的連接字串示例：

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

如需詳細資訊，請參閱[設定 Azure 儲存體連接字串](storage-configure-connection-string.md)。

### <a name="custom-storage-accounts-and-keys"></a>自訂存儲帳戶和金鑰

Azurite 通過設置`AZURITE_ACCOUNTS`以下格式的環境變數支援自訂存儲帳戶名稱和金鑰： `account1:key1[:key2];account2:key1[:key2];...`。

例如，使用具有一個金鑰的自訂存儲帳戶：

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

或者使用多個存儲帳戶，每個帳戶有 2 個金鑰：

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

預設情況下，Azurite 每分鐘都會從環境變數刷新自訂帳戶名稱和金鑰。 借助此功能，您可以動態旋轉帳戶金鑰，或添加新存儲帳戶，而無需重新開機 Azurite。

> [!NOTE]
> 設置自訂`devstoreaccount1`存儲帳戶時，將禁用預設存儲帳戶。

> [!NOTE]
> 使用自訂帳戶名稱和鍵時，請相應地更新連接字串。

> [!NOTE]
> 使用`export`關鍵字在 Linux 環境中設置環境變數，`set`在 Windows 中使用。

### <a name="storage-explorer"></a>儲存體總管

在 Azure 存儲資源管理器中，通過按一下 **"添加帳戶**"圖示連接到 Azurite，然後選擇"**附加到本地模擬器**"，然後按一下"**連接**"。

## <a name="differences-between-azurite-and-azure-storage"></a>Azurite 和 Azure 存儲之間的差異

Azurite 的本地實例和雲中的 Azure 存儲帳戶之間存在功能差異。

### <a name="endpoint-and-connection-url"></a>端點和連接 URL

Azurite 的服務終結點不同于 Azure 存儲帳戶的終結點。 本地電腦不執行功能變數名稱解析，要求 Azurite 終結點為本地位址。

在 Azure 存儲帳戶中定址資源時，帳戶名稱是 URI 主機名稱的一部分。 要處理的資源是 URI 路徑的一部分：

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

以下 URI 是 Azure 存儲帳戶中 Blob 的有效位址：

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

由於本地電腦不執行功能變數名稱解析，因此帳戶名稱是 URI 路徑的一部分，而不是主機名稱。 對 Azurite 中的資源使用以下 URI 格式：

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

以下位址可用於訪問 Azurite 中的 blob：

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>縮放和性能

Azurite 不是可擴展的存儲服務，不支援大量併發用戶端。 沒有性能保證。 Azurite 用於開發和測試目的。

### <a name="error-handling"></a>錯誤處理

Azurite 與 Azure 存儲錯誤處理邏輯對齊，但存在差異。 例如，錯誤訊息可能不同，而錯誤狀態碼對齊。

### <a name="ra-grs"></a>RA-GRS

Azurite 支援讀取存取異地冗余複製 （RA-GRS）。 對於存儲資源，通過將 **-輔助**位置追加到帳戶名稱來訪問輔助位置。 例如，以下位址可用於使用 Azurite 中的唯讀次要資料庫訪問 Blob：

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>阿祖裡特是開源的

歡迎為阿蘇雷特提供捐助和建議。 轉到 Azurite [GitHub 專案](https://github.com/Azure/Azurite/projects)頁面或[GitHub 問題](https://github.com/Azure/Azurite/issues)，查看我們正在跟蹤的里程碑和工作專案，以跟蹤即將推出的功能和錯誤修復。 在 GitHub 中也跟蹤詳細的工作項。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 存儲模擬器進行開發和測試](storage-use-emulator.md)文檔的舊 Azure 存儲模擬器，該模擬程式正被 Azurite 取代。
* [配置 Azure 存儲連接字串](storage-configure-connection-string.md)說明如何組裝有效的 Azure Storage 連接字串。
