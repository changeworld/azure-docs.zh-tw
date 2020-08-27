---
title: 在入口網站中設定應用程式
description: 瞭解如何在 Azure 入口網站中設定 App Service 應用程式的一般設定。 應用程式設定、連接字串、平臺、語言堆疊、容器等。
keywords: azure app service，web 應用程式，應用程式設定，環境變數
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.topic: article
ms.date: 08/13/2019
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 4990862bacbf2afe2d4b5f39c2b9d31a7c48b78e
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962888"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>設定 Azure 入口網站中的 App Service 應用程式

本主題說明如何使用 [Azure 入口網站]來設定 web 應用程式、行動後端或 API 應用程式的一般設定。

## <a name="configure-app-settings"></a>進行應用程式設定

在 App Service 中，應用程式設定是以環境變數形式傳遞至應用程式程式碼的變數。 針對 Linux 應用程式和自訂容器，App Service 會使用旗標將應用程式設定傳遞至容器， `--env` 以設定容器中的環境變數。

在 [Azure 入口網站]中，搜尋並選取 [應用程式服務]，然後選取您的應用程式。 

![搜尋應用程式服務](./media/configure-common/search-for-app-services.png)

在應用程式的左側功能表中，**選取 [**  >  **設定應用程式設定**]。

![應用程式設定](./media/configure-common/open-ui.png)

針對 ASP.NET 和 ASP.NET Core 開發人員，在 App Service 中設定應用程式設定，就像是在 `<appSettings>` *Web.config* 或 *appsettings.js*中設定它們，但 App Service 中的值會覆寫 *Web.config* 或 *appsettings.js*中的值。 您可以保留開發設定 (例如， *Web.config* 或 *appsettings.js*中的本機 MySQL 密碼) ，但生產秘密 (例如，Azure MySQL 資料庫密碼) 在 App Service 中安全。 當您在本機進行偵錯工具時，相同的程式碼會使用您的開發設定，並在部署至 Azure 時使用您的生產秘密。

同樣地，其他語言堆疊會在執行時間取得應用程式設定作為環境變數。 如需語言堆疊的特定步驟，請參閱：

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [自訂容器](configure-custom-container.md#configure-environment-variables)

應用程式設定在儲存時一律加密 (待用加密)。

> [!NOTE]
> 您也可以使用[Key Vault 參考](app-service-key-vault-references.md)，從[Key Vault](../key-vault/index.yml)解析應用程式設定。

### <a name="show-hidden-values"></a>顯示隱藏的值

根據預設，應用程式設定的值會在入口網站中隱藏，以獲得安全性。 若要查看應用程式設定的隱藏值，請按一下該設定的 [ **值** ] 欄位。 若要查看所有應用程式設定的值，請按一下 [ **顯示值** ] 按鈕。

### <a name="add-or-edit"></a>新增或編輯

若要加入新的應用程式設定，請按一下 [ **新增應用程式設定**]。 在對話方塊中，您可以將設定放在 [目前的](deploy-staging-slots.md#which-settings-are-swapped)位置。

若要編輯設定，請按一下右側的 [ **編輯** ] 按鈕。

完成時，按一下 [ **更新**]。 別**忘了按一下**[設定] 頁面中的 [重新**存**回]。

> [!NOTE]
> 在預設的 Linux 容器或自訂 Linux 容器中，應用程式設定名稱中的任何嵌套 JSON 索引鍵結構都必須 `ApplicationInsights:InstrumentationKey` 在 App Service 中設定，以作為索引 `ApplicationInsights__InstrumentationKey` 鍵名稱。 換句話說，您 `:` 應該將任何 `__` (雙底線) 取代。
>

### <a name="edit-in-bulk"></a>大量編輯

若要大量新增或編輯應用程式設定，請按一下 [ **Advanced edit** ] \ （編輯 \）按鈕。 完成時，按一下 [ **更新**]。 別**忘了按一下**[設定] 頁面中的 [重新**存**回]。

應用程式設定具有下列 JSON 格式：

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

在 [Azure 入口網站]中，搜尋並選取 [應用程式服務]，然後選取您的應用程式。 在應用程式的左側功能表中，**選取 [**  >  **設定應用程式設定**]。

![應用程式設定](./media/configure-common/open-ui.png)

若為 ASP.NET 和 ASP.NET Core 開發人員，在 App Service 中設定連接字串，就像在Web.config中設定連接字串 `<connectionStrings>` ，但您在 App Service 中設定的值會覆寫*Web.config*中的連接字串。 *Web.config*您可以保留開發設定 (例如，資料庫檔案) *Web.config*和生產秘密 (例如，SQL Database 安全地) 認證。 當您在本機進行偵錯工具時，相同的程式碼會使用您的開發設定，並在部署至 Azure 時使用您的生產秘密。

針對其他語言堆疊，最好改為使用 [應用程式設定](#configure-app-settings) ，因為連接字串需要變數索引鍵的特殊格式，才能存取這些值。 以下是一個例外狀況，不過，如果您在應用程式中設定其連接字串，則會將某些 Azure 資料庫類型與應用程式一起備份。 如需詳細資訊，請參閱已 [備份的內容](manage-backup.md#what-gets-backed-up)。 如果您不需要此自動備份，請使用應用程式設定。

在執行時間，連接字串會以環境變數的形式提供，並在前面加上下列連線類型：

* Server `SQLCONNSTR_`  
* MySQL： `MYSQLCONNSTR_` 
* SQLAzure `SQLAZURECONNSTR_` 
* 自訂：`CUSTOMCONNSTR_`
* 于 postgresql `POSTGRESQLCONNSTR_`  

例如，名為 *connectionstring1* 的 MySql 連接字串可以存取為環境變數 `MYSQLCONNSTR_connectionString1` 。 如需語言堆疊的特定步驟，請參閱：

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [自訂容器](configure-custom-container.md#configure-environment-variables)

連接字串在儲存時一律加密 (待用加密)。

> [!NOTE]
> 使用[Key Vault 參考](app-service-key-vault-references.md)，也可以從[Key Vault](../key-vault/index.yml)解析連接字串。

### <a name="show-hidden-values"></a>顯示隱藏的值

根據預設，連接字串的值會在入口網站中隱藏，以獲得安全性。 若要查看連接字串的隱藏值，只需按一下該字串的 **值** 欄位。 若要查看所有連接字串的值，請按一下 [ **顯示值** ] 按鈕。

### <a name="add-or-edit"></a>新增或編輯

若要加入新的連接字串，請按一下 [ **新增連接字串**]。 在對話方塊中，您可以將 [連接字串放到目前的](deploy-staging-slots.md#which-settings-are-swapped)位置。

若要編輯設定，請按一下右側的 [ **編輯** ] 按鈕。

完成時，按一下 [ **更新**]。 別**忘了按一下**[設定] 頁面中的 [重新**存**回]。

### <a name="edit-in-bulk"></a>大量編輯

若要大量加入或編輯連接字串，請按一下 [ **Advanced edit** ] 按鈕。 完成時，按一下 [ **更新**]。 別**忘了按一下**[設定] 頁面中的 [重新**存**回]。

連接字串具有下列 JSON 格式：

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

在 [Azure 入口網站]中，搜尋並選取 [應用程式服務]，然後選取您的應用程式。 在應用程式的左側功能表中，**選取**  >  **[設定一般設定**]。

![一般設定](./media/configure-common/open-general.png)

在這裡，您可以設定應用程式的一些常見設定。 某些設定需要您 [擴大至更高的定價層](manage-scale-up.md)。

- **堆疊設定**：用來執行應用程式的軟體堆疊，包括語言和 SDK 版本。 針對 Linux 應用程式和自訂容器應用程式，您也可以設定選用的啟動命令或檔案。
- **平臺設定**：可讓您設定裝載平臺的設定，包括：
    - **位數**：32位或64位。
    - **WebSocket 通訊協定**：例如，針對 [ASP.NET SignalR] 或 [socket.io](https://socket.io/)。
    - **Always On**：即使沒有流量，仍會讓應用程式保持載入。 連續 Webjob 或使用 CRON 運算式觸發的 Webjob 都需要它。
      > [!NOTE]
      > 使用 Always On 功能時，前端負載平衡器會將要求傳送至應用程式根目錄。 無法設定 App Service 的應用程式端點。
    - **受控管線版本**： IIS [管線模式]。 如果您有需要舊版 IIS 的繼承應用程式，請將它設定為 [ **傳統** ]。
    - **HTTP 版本**：設為 **2.0** ，以啟用對 [HTTPS/2](https://wikipedia.org/wiki/HTTP/2) 通訊協定的支援。
    > [!NOTE]
    > 大部分的新式瀏覽器僅支援透過 TLS 的 HTTP/2 通訊協定，非加密的流量則會繼續使用 HTTP/1.1。 為確保用戶端瀏覽器會使用 HTTP/2 連接到您的應用程式，請保護您的自訂 DNS 名稱。 如需詳細資訊，請參閱 [Azure App Service 中的使用 TLS/SSL 系結保護自訂 DNS 名稱](configure-ssl-bindings.md)。
    - **ARR 親和性**：在多重實例部署中，確定用戶端會在會話的存留期間路由傳送至相同的實例。 您可以針對無狀態應用程式將此選項設定為 [ **關閉** ]。
- **調試**程式：為 [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug)、 [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)或 [Node.js](configure-language-nodejs.md#debug-remotely) 應用程式啟用遠端偵錯程式。 此選項會在48小時後自動關閉。
- **傳入用戶端憑證**：需要 [雙向驗證](app-service-web-configure-tls-mutual-auth.md)的用戶端憑證。

## <a name="configure-default-documents"></a>設定預設檔

這項設定僅適用于 Windows 應用程式。

在 [Azure 入口網站]中，搜尋並選取 [應用程式服務]，然後選取您的應用程式。 在應用程式的左側功能表中，**選取 [** 設定  >  **預設檔**]。

![預設文件](./media/configure-common/open-documents.png)

預設檔是顯示在網站根 URL 的網頁。 系統會使用清單中第一個相符的檔案。 若要加入新的預設檔，請按一下 [ **新增檔**]。 別忘了按一下 [ **儲存**]。

如果應用程式使用以 URL 為基礎的模組，而不是提供靜態內容，則不需要預設檔。

## <a name="configure-path-mappings"></a>設定路徑對應

在 [Azure 入口網站]中，搜尋並選取 [應用程式服務]，然後選取您的應用程式。 在應用程式的左側功能表中，**選取 [** 設定  >  **路徑**對應]。

![路徑對應](./media/configure-common/open-path.png)

[ **路徑** 對應] 頁面會根據 OS 類型顯示不同的專案。

### <a name="windows-apps-uncontainerized"></a>Windows 應用程式 (uncontainerized) 

針對 Windows 應用程式，您可以自訂 IIS 處理常式對應和虛擬應用程式和目錄。

處理常式對應可讓您新增自訂腳本處理器以處理特定副檔名的要求。 若要加入自訂處理常式，請按一下 [ **新增處理常式**]。 設定處理常式，如下所示：

- **延伸**模組。 您要處理的副檔名，例如* \* php*或*handler.fcgi*。
- **腳本處理器**。 腳本處理器的絕對路徑。 符合副檔名之檔案的要求會由腳本處理器處理。 使用路徑 `D:\home\site\wwwroot` 以指出應用程式的根目錄。
- **引數**。 腳本處理器的選擇性命令列引數。

每個應用程式都有預設的根路徑 (`/`) 對應至 `D:\home\site\wwwroot` ，預設會部署您的程式碼。 如果您的應用程式根目錄位於不同的資料夾中，或您的儲存機制有多個應用程式，您可以在這裡編輯或新增虛擬應用程式和目錄。 按一下 [ **新增虛擬應用程式或目錄**]。

若要設定虛擬應用程式和目錄，請指定每個虛擬目錄及其相對於網站根目錄 () 的相對應實體路徑 `D:\home` 。 或者，您可以選取 [ **應用程式** ] 核取方塊，勾選虛擬目錄做為應用程式。

### <a name="containerized-apps"></a>容器化應用程式

您可以 [為容器化應用程式新增自訂儲存體](configure-connect-to-azure-storage.md)。 容器化應用程式包含所有 Linux 應用程式，以及在 App Service 上執行的 Windows 和 Linux 自訂容器。 按一下 [ **新增] Azure 儲存體掛接** 並設定您的自訂存放裝置，如下所示：

- **名稱**：顯示名稱。
- 設定**選項**：**基本**或**Advanced**。
- **儲存體帳戶**：具有您想要之容器的儲存體帳戶。
- **儲存體類型**： **Azure blob** 或 **Azure 檔案儲存體**。
  > [!NOTE]
  > Windows 容器應用程式僅支援 Azure 檔案儲存體。
- **儲存體容器**：適用于基本設定、您要的容器。
- **共用名稱**：用於 advanced configuration，檔案共用名稱。
- **存取金鑰**：適用于 advanced configuration，存取金鑰。
- **掛接路徑**：容器中用來掛接自訂存放裝置的絕對路徑。

如需詳細資訊，請參閱 [從 App Service 中的容器存取 Azure 儲存體做為網路共用](configure-connect-to-azure-storage.md)。

## <a name="configure-language-stack-settings"></a>設定語言堆疊設定

針對 Linux 應用程式，請參閱：

- [ASP.NET Core](configure-language-dotnetcore.md)
- [Node.js](configure-language-nodejs.md)
- [PHP](configure-language-php.md)
- [Python](configure-language-python.md)
- [Java](configure-language-java.md)
- [Ruby](configure-language-ruby.md)

## <a name="configure-custom-containers"></a>設定自訂容器

請參閱 [為 Azure App Service 設定自訂 Linux 容器](configure-custom-container.md)

## <a name="next-steps"></a>後續步驟

- [在 Azure App Service 中設定自訂網域名稱]
- [在 Azure App Service 中設定預備環境] \(部分機器翻譯\)
- [在 Azure App Service 中使用 TLS/SSL 繫結保護自訂 DNS 名稱](configure-ssl-bindings.md)
- [啟用診斷記錄](troubleshoot-diagnostic-logs.md)
- [在 Azure App Service 中調整應用程式規模]
- [在 Azure App Service 中監視基本概念]
- [使用 Applicationhost.config 變更 applicationHost.config 設定 xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure 入口網站]: https://portal.azure.com/
[在 Azure App Service 中設定自訂網域名稱]: ./app-service-web-tutorial-custom-domain.md
[在 Azure App Service 中設定預備環境]: ./deploy-staging-slots.md \(部分機器翻譯\)
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[在 Azure App Service 中監視基本概念]: ./web-sites-monitor.md
[管線模式]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[在 Azure App Service 中調整應用程式規模]: ./manage-scale-up.md