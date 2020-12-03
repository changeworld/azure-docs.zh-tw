---
title: 設定自訂容器
description: 瞭解如何在 Azure App Service 中設定自訂容器。 本文說明最常見的設定工作。
ms.topic: article
ms.date: 09/22/2020
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 2aece0550d7b78ac4312e71b2671de4a64e4b86b
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96557921"
---
# <a name="configure-a-custom-container-for-azure-app-service"></a>設定 Azure App Service 的自訂容器

本文說明如何將自訂容器設定為在 Azure App Service 上執行。

::: zone pivot="container-windows"

本指南提供在 App Service 中容器化 Windows 應用程式的重要概念和指示。 如果您從未使用過 Azure App Service，請先遵循 [自訂容器快速入門](quickstart-custom-container.md) 和 [教學](tutorial-custom-container.md) 課程。

::: zone-end

::: zone pivot="container-linux"

本指南提供在 App Service 中容器化 Linux 應用程式的重要概念和指示。 如果您從未使用過 Azure App Service，請先遵循 [自訂容器快速入門](quickstart-custom-container.md) 和 [教學](tutorial-custom-container.md) 課程。 另外還有一個 [多容器應用程式快速入門](quickstart-multi-container.md) 和 [教學](tutorial-multi-container-app.md)課程。

::: zone-end

::: zone pivot="container-windows"

## <a name="supported-parent-images"></a>支援的父映射

針對您的自訂 Windows 映像，您必須為您要的架構選擇正確的 [父映射 (基礎映射) ](https://docs.docker.com/develop/develop-images/baseimages/) ：

- 若要部署 .NET Framework 應用程式，請使用以 Windows Server Core [長期維護通道 (LTSC) ](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) 版本為基礎的父映射。 
- 若要部署 .NET Core 應用程式，請根據 Windows Server Nano [半年度維護通道 ](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) 來使用父映射 (SAC) 版本。 

在應用程式啟動期間，下載父映像需要一些時間。 不過，您可以使用下列其中一個已在 Azure App Service 中快取的父映像，以縮短啟動時間：

- [mcr.microsoft.com/windows/servercore](https://hub.docker.com/_/microsoft-windows-servercore)：2004
- [mcr.microsoft.com/windows/servercore](https://hub.docker.com/_/microsoft-windows-servercore)： ltsc2019
- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/)： 4.8-windowsservercore-2004
- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/)： 4.8-windowsservercore-ltsc2019
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/)： 3.1-nanoserver-2004
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/)： 3.1-nanoserver-1909
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/)： 3.1-nanoserver-1903
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/)： 3.1-nanoserver-1809
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/)： 3.1-nanoserver-2004
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/)： 3.1-nanoserver-1909
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/)： 3.1-nanoserver-1903
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/)： 3.1-nanoserver-1809

::: zone-end

## <a name="change-the-docker-image-of-a-custom-container"></a>變更自訂容器的 Docker 映射

若要將現有的自訂容器應用程式從目前的 Docker 映射變更為新映射，請使用下列命令：

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>使用私人登錄中的映射

若要使用私人登錄中的映射（例如 Azure Container Registry），請執行下列命令：

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

針對 *\<username>* 和 *\<password>* ，提供私人登錄帳戶的登入認證。

## <a name="i-dont-see-the-updated-container"></a>我看不到更新的容器

如果您將 Docker 容器設定變更為指向新的容器，可能需要幾分鐘的時間，應用程式才會從新的容器服務 HTTP 要求。 在提取並啟動新的容器時，App Service 繼續提供來自舊容器的要求。 只有當新的容器啟動且準備好接收要求時 App Service 開始將要求傳送給它。

## <a name="how-container-images-are-stored"></a>容器映射的儲存方式

當您第一次在 App Service 中執行自訂 Docker 映射時，App Service 會執行 `docker pull` 並提取所有的映射層。 這些層會儲存在磁片上，就像您在內部部署使用 Docker 一樣。 每次應用程式重新開機時，App Service 都會執行 `docker pull` ，但是只會提取已變更的圖層。 如果沒有任何變更，App Service 會在本機磁片上使用現有的層級。

如果應用程式因為任何原因而變更計算實例，例如擴大和縮小定價層，App Service 必須再次提取所有層級。 如果您相應放大以新增額外的實例，也是如此。 在某些罕見的情況下，應用程式實例可能會在沒有調整規模作業的情況下變更。

## <a name="configure-port-number"></a>設定埠號碼

根據預設，App Service 會假設您的自訂容器正在接聽埠80。 如果您的容器接聽不同的埠，請 `WEBSITES_PORT` 在 App Service 應用程式中設定應用程式設定。 您可以透過 [Cloud Shell](https://shell.azure.com)進行設定。 在 Bash 中：

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

在 PowerShell 中：

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_PORT"="8000"}
```

App Service 目前可讓您的容器只針對 HTTP 要求公開一個埠。 

## <a name="configure-environment-variables"></a>設定環境變數

您的自訂容器可以使用需要在外部提供的環境變數。 您可以透過 [Cloud Shell](https://shell.azure.com)傳遞它們。 在 Bash 中：

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings DB_HOST="myownserver.mysql.database.azure.com"
```

在 PowerShell 中：

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"DB_HOST"="myownserver.mysql.database.azure.com"}
```

當您的應用程式執行時，系統會自動將 App Service 應用程式設定插入進程中，以作為環境變數。 

::: zone pivot="container-windows"
若為 IIS 或 .NET Framework (4.0 或更新版本) 基礎的容器，App Service 會將它們插入 `System.ConfigurationManager` 為 .net 應用程式設定和連接字串。 針對其他所有語言或架構，則會以下列其中一個對應的前置詞提供給處理常式的環境變數：

- `APPSETTING_`
- `SQLCONTR_`
- `MYSQLCONTR_`
- `SQLAZURECOSTR_`
- `POSTGRESQLCONTR_`
- `CUSTOMCONNSTR_`

::: zone-end

::: zone pivot="container-linux"

這個方法適用于單一容器應用程式或多容器應用程式，其中的環境變數是在 *>docker-compose.yml. yml* 檔案中指定。

::: zone-end

## <a name="use-persistent-shared-storage"></a>使用持續性共用儲存體

::: zone pivot="container-windows"

您可以使用應用程式檔案系統中的 *C:\home* 目錄，在重新開機時保存檔案，並在實例之間共用檔案。 `C:\home`提供應用程式中的，以讓您的容器應用程式存取持續性儲存體。

停用持續性儲存體時，不會 `C:\home` 保存目錄的寫入。 [Docker 主機記錄和容器記錄](#access-diagnostic-logs) 會儲存在未附加至容器的預設持續性共用存放裝置中。 啟用持續性儲存體時，會保存目錄的所有寫入， `C:\home` 並可由相應放大應用程式的所有實例存取，而且可以在上存取記錄檔 `C:\home\LogFiles` 。

::: zone-end

::: zone pivot="container-linux"

您可以使用應用程式檔案系統中的 */home* 目錄，在重新開機時保存檔案，並在實例之間共用檔案。 `/home`提供應用程式中的，以讓您的容器應用程式存取持續性儲存體。

停用持續性儲存體時， `/home` 不會跨應用程式重新開機或跨多個實例保存寫入目錄。 唯一的例外狀況是 `/home/LogFiles` 用來儲存 Docker 和容器記錄檔的目錄。 啟用持續性儲存體時，會保存目錄的所有寫入， `/home` 並且可供相應放大應用程式的所有實例存取。

::: zone-end

預設會停用持續性儲存體，而不會在應用程式設定中公開設定。 若要啟用它，請透過 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` [Cloud Shell](https://shell.azure.com)設定應用程式設定。 在 Bash 中：

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

在 PowerShell 中：

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=true}
```

> [!NOTE]
> 您也可以 [設定自己的持續性儲存體](configure-connect-to-azure-storage.md)。

## <a name="detect-https-session"></a>偵測 HTTPS 工作階段

App Service 在前端結束 TLS/SSL。 這表示 TLS/SSL 要求永遠不會進入您的應用程式。 您不需要，也不應該在您的應用程式中執行任何對 TLS/SSL 的支援。 

前端位於 Azure 資料中心內。 如果您使用 TLS/SSL 搭配您的應用程式，您的網際網路流量一律會安全地加密。

::: zone pivot="container-windows"

## <a name="customize-aspnet-machine-key-injection"></a>自訂 ASP.NET 機密鑰插入

 在容器啟動期間，系統會將自動產生的金鑰插入容器中，作為 ASP.NET 密碼編譯常式的電腦金鑰。 您可以藉由尋找下列環境變數， [在容器中找到這些金鑰](#connect-to-the-container) ： `MACHINEKEY_Decryption` 、 `MACHINEKEY_DecryptionKey` 、 `MACHINEKEY_ValidationKey` 、 `MACHINEKEY_Validation` 。 

如果您的應用程式相依于每次重新開機，新的金鑰可能會重設 ASP.NET 表單驗證和查看狀態。 為了避免自動重新產生金鑰，請 [以手動方式將它們設定為 App Service 應用程式](#configure-environment-variables)設定。 

## <a name="connect-to-the-container"></a>連接到容器

您可以流覽至，直接連接到您的 Windows 容器以進行診斷工作 `https://<app-name>.scm.azurewebsites.net/DebugConsole` 。 運作方式如下：

- Debug 主控台可讓您執行互動式命令，例如啟動 PowerShell 會話、檢查登錄機碼，以及流覽整個容器檔案系統。
- 它會與上面的圖形瀏覽器分開運作，而這只會顯示您 [共用儲存區](#use-persistent-shared-storage)中的檔案。
- 在相應放大的應用程式中，debug 主控台會連線至其中一個容器實例。 您可以從上方功能表的 [ **實例** ] 下拉式清單中選取不同的實例。
- 當您的應用程式重新開機時，您在主控台內對容器所做的任何變更都 *不* 會保存 (除非共用儲存體) 中的變更，因為它不是 Docker 映射的一部分。 若要保存您的變更，例如登錄設定和軟體安裝，請讓它們成為 Dockerfile 的一部分。

## <a name="access-diagnostic-logs"></a>存取診斷記錄

App Service 會記錄 Docker 主機的動作，以及來自容器內的活動。 預設會提供 Docker 主機 (platform logs) 的記錄，但需要手動啟用來自容器內的應用程式記錄檔或網頁伺服器記錄。 如需詳細資訊，請參閱 [啟用應用程式記錄](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) 和 [啟用 web 伺服器記錄](troubleshoot-diagnostic-logs.md#enable-web-server-logging)。 

有幾種方式可以存取 Docker 記錄：

- [Azure 入口網站](#in-azure-portal)
- [從 Kudu 主控台](#from-the-kudu-console)
- [使用 Kudu API](#with-the-kudu-api)
- [將記錄傳送至 Azure 監視器](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)

### <a name="in-azure-portal"></a>Azure 入口網站

Docker 記錄會顯示在入口網站的應用程式 [ **容器設定** ] 頁面中。 記錄會被截斷，但您可以按一下 [ **下載**] 來下載所有記錄。 

### <a name="from-the-kudu-console"></a>從 Kudu 主控台

流覽至 `https://<app-name>.scm.azurewebsites.net/DebugConsole` 並按一下 [ **LogFiles** 記錄檔] 資料夾，以查看個別記錄檔。 若要下載整個記錄檔目錄，請 **按一下目錄名稱** 左側的 [ **下載** ] 圖示。 您也可以使用 FTP 用戶端來存取這個資料夾。

在主控台終端機中，預設無法存取 `C:\home\LogFiles` 資料夾，因為未啟用持續性共用存放裝置。 若要在主控台終端機中啟用此行為，請 [啟用持續性共用存放裝置](#use-persistent-shared-storage)。

如果您嘗試使用 FTP 用戶端來下載目前使用中的 Docker 記錄檔，可能會因為檔案鎖定而出現錯誤。

### <a name="with-the-kudu-api"></a>使用 Kudu API

直接流覽至 `https://<app-name>.scm.azurewebsites.net/api/logs/docker` 以查看 Docker 記錄的中繼資料。 您可能會看到列出一個以上的記錄檔，而該 `href` 屬性可讓您直接下載記錄檔。 

若要在單一 ZIP 檔案中下載所有記錄檔，請存取 `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip` 。

## <a name="customize-container-memory"></a>自訂容器記憶體

根據預設，所有部署在 Azure App Service 中的 Windows 容器會限制為 1 GB RAM。 您可以透過 `WEBSITE_MEMORY_LIMIT_MB` [Cloud Shell](https://shell.azure.com)提供應用程式設定，來變更此值。 在 Bash 中：

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_MEMORY_LIMIT_MB=2000
```

在 PowerShell 中：

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_MEMORY_LIMIT_MB"=2000}
```

值的定義為 MB，而且必須小於或等於主機的總實體記憶體。 例如，在具有 8 GB RAM 的 App Service 方案中， `WEBSITE_MEMORY_LIMIT_MB` 所有應用程式的累積總計不得超過 8 gb。 如需每個定價層有多少可用記憶體的相關資訊，請參閱「高階 **容器 (Windows) 規劃**」一節的 [App Service 定價](https://azure.microsoft.com/pricing/details/app-service/windows/)。

## <a name="customize-the-number-of-compute-cores"></a>自訂計算核心數目

根據預設，Windows 容器會針對您選擇的定價層，以所有可用的核心執行。 例如，您可能想要減少預備位置所使用的核心數目。 若要減少容器所使用的核心數目，請將 `WEBSITE_CPU_CORES_LIMIT` 應用程式設定設為慣用的核心數目。 您可以透過 [Cloud Shell](https://shell.azure.com)進行設定。 在 Bash 中：

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --slot staging --settings WEBSITE_CPU_CORES_LIMIT=1
```

在 PowerShell 中：

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_CPU_CORES_LIMIT"=1}
```

> [!NOTE]
> 更新應用程式設定會觸發自動重新開機，導致最短的停機時間。 針對生產應用程式，請考慮將它交換到預備位置、變更預備位置中的應用程式設定，然後將它交換回生產環境。

前往 Kudu 主控台 (`https://<app-name>.scm.azurewebsites.net`) ，然後使用 PowerShell 輸入下列命令，以確認調整的數位。 每個命令都會輸出一個數位。

```PowerShell
Get-ComputerInfo | ft CsNumberOfLogicalProcessors # Total number of enabled logical processors. Disabled processors are excluded.
Get-ComputerInfo | ft CsNumberOfProcessors # Number of physical processors.
```

處理器可能是多核心或超執行緒處理器。 如需每個定價層的可用核心數目的相關資訊，請參閱「高階 **容器 (Windows) 方案**」一節的 [App Service 定價](https://azure.microsoft.com/pricing/details/app-service/windows/)。

## <a name="customize-health-ping-behavior"></a>自訂健全狀況偵測行為

App Service 會將容器視為在容器啟動時成功啟動，並回應 HTTP ping。 健康情況 ping 要求包含標頭 `User-Agent= "App Service Hyper-V Container Availability Check"` 。 如果容器啟動，但在一段時間後沒有回應 ping，App Service 會在 Docker 記錄檔中記錄事件，指出容器未啟動。 

如果您的應用程式需要大量資源，容器可能不會及時回應 HTTP 偵測。 若要控制 HTTP ping 失敗時的動作，請設定 `CONTAINER_AVAILABILITY_CHECK_MODE` 應用程式設定。 您可以透過 [Cloud Shell](https://shell.azure.com)進行設定。 在 Bash 中：

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings CONTAINER_AVAILABILITY_CHECK_MODE="ReportOnly"
```

在 PowerShell 中：

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"CONTAINER_AVAILABILITY_CHECK_MODE"="ReportOnly"}
```

下表顯示可能的值：

| 值 | 說明 |
| - | - |
| **修復** | 連續三次可用性檢查後重新開機容器 |
| **ReportOnly** | 預設值。 在三次連續的可用性檢查之後，請勿重新開機容器，但在容器的 Docker 記錄中報告。 |
| **關閉** | 請勿檢查可用性。 |

## <a name="support-for-group-managed-service-accounts"></a>群組受管理服務帳戶的支援

App Service 中的 Windows 容器目前不支援 (Gmsa) 的群組受管理的服務帳戶。

::: zone-end

::: zone pivot="container-linux"

## <a name="enable-ssh"></a>啟用 SSH

SSH 可讓容器和用戶端之間進行安全通訊。 為了讓自訂容器支援 SSH，您必須將它新增至 Dockerfile 本身。

> [!TIP]
> 所有內建 Linux 容器都已在其映射存放庫中新增 SSH 指令。 您可以使用 [Node.js 10.14 存放庫](https://github.com/Azure-App-Service/node/blob/master/10.14) 進行下列指示，以瞭解其如何在該處啟用。

- 使用 [執行](https://docs.docker.com/engine/reference/builder/#run) 指令來安裝 SSH 伺服器，並將根帳號的密碼設定為 `"Docker!"` 。 例如，針對以 [Alpine Linux](https://hub.docker.com/_/alpine)為基礎的映射，您需要下列命令：

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    此組態不允許容器的外部連線。 SSH 只能透過 `https://<app-name>.scm.azurewebsites.net` 發佈認證進行驗證。

- 將 [此 sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) 檔案新增至您的映射存放庫，並使用 [複製](https://docs.docker.com/engine/reference/builder/#copy) 指示將檔案複製到 */etc/ssh/* 目錄。 如需 *sshd_config* 檔案的詳細資訊，請參閱 [OpenBSD](https://man.openbsd.org/sshd_config)檔。

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > sshd_config 檔案必須包含下列項目︰
    > - `Ciphers` 必須在此清單中包含至少一個項目：`aes128-cbc,3des-cbc,aes256-cbc`。
    > - `MACs` 必須在此清單中包含至少一個項目：`hmac-sha1,hmac-sha1-96`。

- 使用 [公開](https://docs.docker.com/engine/reference/builder/#expose) 指令來開啟容器中的埠2222。 雖然已知根密碼，但無法從網際網路存取埠2222。 只有私人虛擬網路的橋接器網路內的容器可以存取它。

    ```Dockerfile
    EXPOSE 80 2222
    ```

- 在您容器的啟動腳本中，啟動 SSH 伺服器。

    ```bash
    /usr/sbin/sshd
    ```

    如需範例，請參閱預設的 [Node.js 10.14 容器](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) 如何啟動 SSH 伺服器。

## <a name="access-diagnostic-logs"></a>存取診斷記錄

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="configure-multi-container-apps"></a>設定多容器應用程式

- [在 Docker Compose 中使用持續性儲存體](#use-persistent-storage-in-docker-compose)
- [預覽限制](#preview-limitations)
- [Docker Compose 選項](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>在 Docker Compose 中使用持續性儲存體

WordPress 等多容器應用程式需要持續性儲存體才能正常運作。 若要啟用它，您的 Docker Compose 設定必須指向容器 *以外* 的儲存位置。 容器內的儲存位置不會在應用程式重新開機後保存變更。

`WEBSITES_ENABLE_APP_SERVICE_STORAGE`使用[Cloud Shell](https://shell.azure.com)中的[az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)命令來設定應用程式設定，以啟用持續性儲存體。

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

在您的 *>docker-compose.yml. yml* 檔案中，將 `volumes` 選項對應至 `${WEBAPP_STORAGE_HOME}` 。 

`WEBAPP_STORAGE_HOME` 是 App Service 中與您應用程式的永續性儲存體相對應的環境變數。 例如：

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>預覽限制

多容器目前為預覽狀態。 下列 App Service 平臺功能不受支援：

- 驗證 / 授權
- 受控識別
- CORS

### <a name="docker-compose-options"></a>Docker Compose 選項

下列清單顯示支援和不支援的 Docker Compose 設定選項：

#### <a name="supported-options"></a>支援的選項

- 命令
- entrypoint
- 環境
- image
- 連接埠
- restart
- 服務
- 磁碟區

#### <a name="unsupported-options"></a>不支援的選項

- build (不允許)
- depends_on (已忽略)
- networks (已忽略)
- secrets (已忽略)
- 80 和 8080 以外的連接埠 (已忽略)

> [!NOTE]
> 未明確呼叫的任何其他選項都會忽略公開預覽。

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用自訂容器將自訂軟體遷移至 Azure App Service](tutorial-custom-container.md)

::: zone pivot="container-linux"

> [!div class="nextstepaction"]
> [教學課程：多容器 WordPress 應用程式](tutorial-multi-container-app.md)

::: zone-end

或者，請參閱其他資源：

[在 Windows/Linux 容器中載入憑證](configure-ssl-certificate-in-code.md#load-certificate-in-linuxwindows-containers)
