---
title: 在門戶中配置應用
description: 瞭解如何在 Azure 門戶中為應用服務應用配置常見設置。 應用設置、連接字串、平臺、語言堆疊、容器等。
keywords: azure 應用服務、Web 應用、應用設置、環境變數
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.topic: article
ms.date: 08/13/2019
ms.custom: seodec18
ms.openlocfilehash: ce0a170a629f347e2687a2e9f63fb3438fe2bd2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280166"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>在 Azure 門戶中配置應用服務應用

本主題介紹如何使用[Azure 門戶]為 Web 應用、移動後端或 API 應用配置常見設置。

## <a name="configure-app-settings"></a>進行應用程式設定

在應用服務中，應用設置是作為環境變數傳遞給應用程式代碼的變數。 對於 Linux 應用和自訂容器，應用服務使用`--env`標誌將應用設置傳遞給容器，以在容器中設置環境變數。

在[Azure 門戶]中，搜索並選擇**應用服務**，然後選擇應用。 

![搜索應用服務](./media/configure-common/search-for-app-services.png)

在應用的左側功能表中，選擇 **"配置** > **應用程式"設置**。

![應用程式設定](./media/configure-common/open-ui.png)

對於`<appSettings>`ASP.NET和ASP.NET核心開發人員，在應用服務中設置應用設置就像在*Web.config*或*appvalue.json*中設置它們一樣，但應用服務中的值將覆蓋*Web.config*或*appvalue.json*中的值。 您可以在*Web.config*或*appsettings.json*中保持開發設置（例如本地 MySQL 密碼）的安全，但在應用服務中保證生產機密（例如，Azure MySQL 資料庫密碼）的安全。 在本地調試時，相同的代碼使用開發設置，並在部署到 Azure 時使用生產機密。

同樣，其他語言堆疊在運行時將應用設置作為環境變數獲取。 有關特定于語言堆疊的步驟，請參閱：

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [Php](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [JAVA](containers/configure-language-java.md#data-sources)
- [紅寶石](containers/configure-language-ruby.md#access-environment-variables)
- [自訂容器](containers/configure-custom-container.md#configure-environment-variables)

應用程式設定在儲存時一律加密 (待用加密)。

> [!NOTE]
> 應用設置也可以使用[金鑰保存庫引用](app-service-key-vault-references.md)從[金鑰保存庫](/azure/key-vault/)解析。

### <a name="show-hidden-values"></a>顯示隱藏值

預設情況下，出於安全考慮，應用設置的值將隱藏在門戶中。 要查看應用設置的隱藏值，請按一下該設置的 **"值"** 欄位。 要查看所有應用設置的值，請按一下"**顯示值"** 按鈕。

### <a name="add-or-edit"></a>添加或編輯

要添加新的應用設置，請按一下 **"新建應用程式"設置**。 在對話方塊中，您可以將[設置粘定在當前插槽](deploy-staging-slots.md#which-settings-are-swapped)上。

要編輯設置，請按一下右側的 **"編輯"** 按鈕。

完成後，按一下 **"更新**"。 不要忘記在 **"配置**"頁中按一下"**保存**"。

> [!NOTE]
> 在預設 Linux 容器或自訂 Linux 容器中，應用設置名稱中任何嵌套的 JSON`ApplicationInsights:InstrumentationKey`金鑰結構都需要在應用服務中`ApplicationInsights__InstrumentationKey`配置為金鑰名稱。 換句話說，任何一`:`個都應替換為`__`（雙底線）。
>

### <a name="edit-in-bulk"></a>大量編輯

要大量新增或編輯應用設置，請按一下 **"高級編輯**"按鈕。 完成後，按一下 **"更新**"。 不要忘記在 **"配置**"頁中按一下"**保存**"。

應用設置具有以下 JSON 格式：

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

## <a name="configure-connection-strings"></a>設定連接字串

在[Azure 門戶]中，搜索並選擇**應用服務**，然後選擇應用。 在應用的左側功能表中，選擇 **"配置** > **應用程式"設置**。

![應用程式設定](./media/configure-common/open-ui.png)

對於ASP.NET和ASP.NET核心開發人員，在應用服務中設置連接字串就像在`<connectionStrings>`*Web.config*中設置它們一樣，但在應用服務中設置的值將覆蓋*Web.config*中的值。您可以在應用服務中安全地將開發設置（例如資料庫檔案）保存在*Web.config*和生產機密（例如 SQL 資料庫憑據）中。 在本地調試時，相同的代碼使用開發設置，並在部署到 Azure 時使用生產機密。

對於其他語言堆疊，最好改用[應用設置](#configure-app-settings)，因為連接字串需要變數鍵中的特殊格式才能訪問值。 但是，這裡有一個例外：如果在應用中配置其連接字串，則某些 Azure 資料庫類型將與應用一起備份。 有關詳細資訊，請參閱[備份的內容](manage-backup.md#what-gets-backed-up)。 如果您不需要此自動備份，請使用應用設置。

在運行時，連接字串作為環境變數可用，預綴為以下連線類型：

* SQL Server： `SQLCONNSTR_`
* MySQL： `MYSQLCONNSTR_`
* SQL Database： `SQLAZURECONNSTR_`
* 自訂：`CUSTOMCONNSTR_`

例如，可以作為環境變數`MYSQLCONNSTR_connectionString1`訪問名為*connectstring1*的 MySql 連接字串。 有關特定于語言堆疊的步驟，請參閱：

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [Php](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [JAVA](containers/configure-language-java.md#data-sources)
- [紅寶石](containers/configure-language-ruby.md#access-environment-variables)
- [自訂容器](containers/configure-custom-container.md#configure-environment-variables)

連接字串在儲存時一律加密 (待用加密)。

> [!NOTE]
> 連接字串也可以使用[金鑰保存庫引用](app-service-key-vault-references.md)從[金鑰保存庫](/azure/key-vault/)解析。

### <a name="show-hidden-values"></a>顯示隱藏值

預設情況下，連接字串的值隱藏在門戶中，以表示安全性。 要查看連接字串的隱藏值，只需按一下該字串**的值**欄位即可。 要查看所有連接字串的值，請按一下"**顯示值"** 按鈕。

### <a name="add-or-edit"></a>添加或編輯

要添加新的連接字串，請按一下 **"新建連接字串**"。 在對話方塊中，您可以將[連接字串粘到當前插槽](deploy-staging-slots.md#which-settings-are-swapped)。

要編輯設置，請按一下右側的 **"編輯"** 按鈕。

完成後，按一下 **"更新**"。 不要忘記在 **"配置**"頁中按一下"**保存**"。

### <a name="edit-in-bulk"></a>大量編輯

要大量新增或編輯連接字串，請按一下 **"高級編輯**"按鈕。 完成後，按一下 **"更新**"。 不要忘記在 **"配置**"頁中按一下"**保存**"。

連接字串具有以下 JSON 格式：

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>設定一般設定

在[Azure 門戶]中，搜索並選擇**應用服務**，然後選擇應用。 在應用的左側功能表中，選擇 **"配置** > **常規設置**"。

![一般設定](./media/configure-common/open-general.png)

在這裡，您可以為應用配置一些常見設置。 某些設置要求您[向上擴展到更高的定價層](manage-scale-up.md)。

- **堆疊設置**：要運行應用的軟體堆疊，包括語言和 SDK 版本。 對於 Linux 應用和自訂容器應用，您還可以設置可選的啟動命令或檔。
- **平臺設置**：允許您為託管平台佈建設置，包括：
    - **位：** 32 位或 64 位。
    - **WebSocket 協定**：例如[，對於ASP.NET信號R]或[socket.io。](https://socket.io/)
    - **始終打開**：即使沒有流量，也能保持應用程式載入。 對於使用 CRON 運算式觸發的連續 Web 作業或 Web 作業，都需要它。
      > [!NOTE]
      > 使用"始終打開"功能時，無法控制終結點。 它始終向應用程式根發送請求。
    - **託管管道版本**：IIS[管線模式]。 如果您有需要舊版本的 IIS 的舊版應用，則將其設置為**經典**版。
    - **HTTP 版本**： 設置為**2.0**以啟用對[HTTPS/2](https://wikipedia.org/wiki/HTTP/2)協定的支援。
    > [!NOTE]
    > 大部分的新式瀏覽器僅支援透過 TLS 的 HTTP/2 通訊協定，非加密的流量則會繼續使用 HTTP/1.1。 為確保用戶端瀏覽器使用 HTTP/2 連接到你的應用，[請使用 Azure 應用服務 中的 SSL 綁定來保護自訂 DNS 名稱](configure-ssl-bindings.md)。
    - **ARR 關聯**：在多實例部署中，確保用戶端在會話的生命週期內路由到同一實例。 您可以將此選項設置為"為無狀態應用程式**關閉**"。
- **調試**：為[ASP.NET、ASP.NET核心](/visualstudio/debugger/remote-debugging-azure)應用或[ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug)[Node.js](containers/configure-language-nodejs.md#debug-remotely)應用啟用遠端偵錯。 此選項在 48 小時後自動關閉。
- **傳入用戶端憑證**：在[相互身份驗證](app-service-web-configure-tls-mutual-auth.md)中需要用戶端憑證。

## <a name="configure-default-documents"></a>配置預設文件

此設置僅適用于 Windows 應用。

在[Azure 門戶]中，搜索並選擇**應用服務**，然後選擇應用。 在應用的左側功能表中，選擇 **"配置** > **預設文件**"。

![預設文件](./media/configure-common/open-documents.png)

預設文件是在網站的根 URL 中顯示的網頁。 系統會使用清單中第一個相符的檔案。 要添加新預設文件，請按一下 **"新建文檔**"。 不要忘記按一下"**保存**"。

如果應用使用基於 URL 路由的模組而不是提供靜態內容，則不需要預設文件。

## <a name="configure-path-mappings"></a>配置路徑映射

在[Azure 門戶]中，搜索並選擇**應用服務**，然後選擇應用。 在應用的左側功能表中，選擇 **"配置** > **路徑"映射**。

![路徑映射](./media/configure-common/open-path.png)

"**路徑映射"** 頁根據作業系統類型顯示不同的東西。

### <a name="windows-apps-uncontainerized"></a>Windows 應用（未容器化）

對於 Windows 應用，您可以自訂 IIS 處理常式映射以及虛擬應用程式和目錄。

處理常式映射允許您添加自訂腳本處理器來處理特定檔副檔名的請求。 要添加自訂處理常式，請按一下 **"新建"處理常式**。 按如下方式配置處理常式：

- **副檔名**。 要處理的檔副檔名，如*\*.php*或*處理常式.fcgi*。
- **腳本處理器**。 腳本處理器的絕對路徑。 腳本處理器處理對與檔副檔名匹配的檔的請求。 使用路徑 `D:\home\site\wwwroot` 以指出應用程式的根目錄。
- **參數**. 腳本處理器的可選命令列參數。

每個應用都有預設根路徑 （`/`）， 映射到`D:\home\site\wwwroot`， 預設情況下，代碼部署在何處。 如果應用根位於其他資料夾中，或者存儲庫有多個應用程式，則可以在此處編輯或添加虛擬應用程式和目錄。 按一下 **"新建虛擬應用程式或目錄**"。

要配置虛擬應用程式和目錄，請指定每個虛擬目錄及其相對於網站根 （`D:\home`的實體路徑 ） 的實體路徑。 或者，您可以選取 [ **應用程式** ] 核取方塊，勾選虛擬目錄做為應用程式。

### <a name="containerized-apps"></a>容器化應用

您可以[為容器化應用添加自訂存儲](containers/how-to-serve-content-from-azure-storage.md)。 容器化應用包括所有 Linux 應用，以及應用服務上運行的 Windows 和 Linux 自訂容器。 按一下 **"新建 Azure 存儲裝載**"並按如下方式配置自訂存儲：

- **名稱**：顯示名稱。
- **配置選項**：**基本**或**高級**。
- **存儲帳戶**：具有所需容器的存儲帳戶。
- **存儲類型** **：Azure Blob 或** **Azure 檔**。
  > [!NOTE]
  > Windows 容器應用僅支援 Azure 檔。
- **存儲容器**：對於基本配置，所需的容器。
- **共用名稱稱**：對於高級配置，檔共用名稱稱。
- **訪問金鑰**：對於高級配置，訪問金鑰。
- **裝載路徑**：用於裝載自訂存儲的容器中的絕對路徑。

如需詳細資訊，請參閱[從 Azure 儲存體在 Linux 上的 App Service 中提供內容](containers/how-to-serve-content-from-azure-storage.md)。

## <a name="configure-language-stack-settings"></a>配置語言堆疊設置

對於 Linux 應用，請參閱：

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [Php](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [JAVA](containers/configure-language-java.md)
- [紅寶石](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>配置自訂容器

請參閱[為 Azure 應用服務配置自訂 Linux 容器](containers/configure-custom-container.md)

## <a name="next-steps"></a>後續步驟

- [在 Azure App Service 中設定自訂網域名稱]
- [在 Azure App Service 中設定預備環境]
- [在 Azure App Service 中使用 SSL 繫結保護自訂 DNS 名稱](configure-ssl-bindings.md)
- [啟用診斷日誌](troubleshoot-diagnostic-logs.md)
- [在 Azure App Service 中調整應用程式規模]
- [在 Azure App Service 中監視基本概念]
- [使用應用程式Host.xdt更改應用程式Host.config設置](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure 門戶]: https://portal.azure.com/
[在 Azure App Service 中設定自訂網域名稱]: ./app-service-web-tutorial-custom-domain.md
[在 Azure App Service 中設定預備環境]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[在 Azure App Service 中監視基本概念]: ./web-sites-monitor.md
[管線模式]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[在 Azure App Service 中調整應用程式規模]: ./manage-scale-up.md
