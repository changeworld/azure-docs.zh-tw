---
title: 使用 FTP/S 部署內容
description: 了解如何使用 FTP 或 FTPS 將您的應用程式部署至 Azure App Service。 通過禁用未加密的 FTP 提高網站安全性。
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 09/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: fcc7c5b8fa182cace6e3dae0b1cae4cd41c5dcb9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532576"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>使用 FTP/S 將您的應用程式部署至 Azure App Service

這篇文章說明如何使用 FTP 或 FTPS 將您的 Web 應用程式、行動裝置應用程式後端或 API 應用程式部署到 [Azure App Service (英文)](https://go.microsoft.com/fwlink/?LinkId=529714)。

您應用程式的 FTP/S 端點已經啟動。 啟用 FTP/S 部署不需要任何組態。

## <a name="open-ftp-dashboard"></a>開啟 FTP 儀表板

1. 在[Azure 門戶](https://portal.azure.com)中,搜尋並選擇**應用服務**。

    ![搜索應用服務。](media/app-service-continuous-deployment/search-for-app-services.png)

2. 選擇要部署的 Web 應用。

    ![選擇你的應用。](media/app-service-continuous-deployment/select-your-app.png)

3. 選擇**部署中心** > **FTP** > **儀表板**。

    ![開啟 FTP 儀表板](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>取得 FTP 連線資訊

在 FTP 儀表板中,選擇 **「複製」** 以複製 FTPS 終結點和應用認證。

![複製 FTP 資訊](./media/app-service-deploy-ftp/ftp-dashboard.png)

建議您使用**應用程式認證**來部署到您的應用程式，因為它對於每個應用程式都是唯一的。 不過，如果您按一下 [使用者認證]****，則可以設定使用者層級的認證，以將其用於 FTP/S 登入到訂用帳戶中的所有 App Service 應用程式。

> [!NOTE]
> 使用使用者級認證對 FTP/FTPS 終結點進行身份驗證需要以以下格式進行使用者名稱: 
>
>`<app-name>\<user-name>`
>
> 由於使用者級憑據連結到使用者而不是特定資源,因此使用者名必須採用此格式才能將登錄操作定向到正確的應用終結點。
>

## <a name="deploy-files-to-azure"></a>將檔案部署至 Azure

1. 從您的 FTP 用戶端 (例如 [Visual Studio](https://www.visualstudio.com/vs/community/)、[Cyberduck](https://cyberduck.io/) 或 [WinSCP](https://winscp.net/index.php))，使用您所蒐集的連線資訊來連線到您的應用程式。
2. 將您的檔案和其個別的目錄結構複製到 Azure 中的 [**/site/wwwroot** 目錄](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (或 WebJobs 的 **/site/wwwroot/App_Data/Jobs/** 目錄)。
3. 瀏覽至您的應用程式 URL，以確認應用程式運作正常。 

> [!NOTE] 
> 與 [Git 型部署](deploy-local-git.md)不同，FTP 部署不支援下列的部署自動化︰ 
>
> - 相依性還原 (例如 NuGet、NPM、PIP 和編輯器自動化)
> - .NET 二進位檔的編譯
> - web.config 的產生 (此處是 [Node.js 範例](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> 請在本機電腦上手動產生這些必要檔案，然後與您的應用程式一起部署這些檔案。
>

## <a name="enforce-ftps"></a>強制使用 FTPS

為提高安全性,應僅允許 FTP 通過 TLS/SSL。 如果您不使用 FTP 部署，您也可以停用 FTP 和 FTPS。

在[Azure 門戶](https://portal.azure.com)中的應用資源頁中,從左側導航中選擇 **「設定** > **常規設置**」。

要禁用未加密的 FTP,請選擇 **「僅限 FTP 狀態的 FTPS」。。** **FTP state** 要完全禁用 FTP 和 FTPS,請選擇 **"禁用**"。 完成時，按一下 [儲存]****。 如果僅使用**FTPS**,則必須透過導航到 Web 應用的**TLS/SSL 設定**邊欄選項卡來強制執行 TLS 1.2 或更高版本。 TLS 1.0 和 1.1 不支援 [僅限 FTPS]**** 功能。

![停用 FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>使用指令碼進行自動化

如果是使用 [Azure CLI](/cli/azure) 進行 FTP 部署，請參閱[建立 Web 應用程式並使用 FTP 部署檔案 (Azure CLI)](./scripts/cli-deploy-ftp.md)。

如果是使用 [Azure PowerShell](/cli/azure) 進行 FTP 部署，請參閱[使用 FTP 將檔案上傳至 Web 應用程式 (PowerShell)](./scripts/powershell-deploy-ftp.md)。

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>疑難排解 FTP 部署

- [如何疑難排解 FTP 部署？](#how-can-i-troubleshoot-ftp-deployment)
- [我無法 FTP 和發佈我的代碼。如何解決此問題?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [如何透過被動模式連線到 Azure App Service 中的 FTP？](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>如何疑難排解 FTP 部署？

疑難排解 FTP 部署的第一個步驟是隔離部署問題與執行階段應用程式問題。

部署問題通常會導致沒有任何檔案或錯誤檔案部署到您的應用程式。 您可以調查 FTP 部署或選取替代部署路徑 (例如原始檔控制)，即可進行疑難排解。

執行階段應用程式問題通常會導致正確的檔案集部署到您的應用程式，但是應用程式行為不正確。 您可以將焦點放在執行階段程式碼行為，並且調查特定失敗路徑，即可進行疑難排解。

若要判斷部署或執行階段問題，請參閱[部署與執行階段問題](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues)。

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>我無法連線到 FTP 及發佈我的程式碼。 如何解決此問題？
請檢查您已輸入正確的主機名稱和[認證](#open-ftp-dashboard)。 此外，檢查防火牆並未封鎖您電腦上的下列 FTP 連接埠：

- FTP 控制連線連接埠︰21
- FTP 資料連線連接埠︰989、10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>如何透過被動模式連線到 Azure App Service 中的 FTP？
Azure App Service 支援透過主動與被動模式進行連線。 建議使用被動模式，因為部署電腦通常位於防火牆背後 (或在作業系統中或是家用或公司網路的一部分)。 請參閱 [WinSCP 文件中的範例](https://winscp.net/docs/ui_login_connection)。 

## <a name="next-steps"></a>後續步驟

如需更多的進階部署案例，請嘗試[使用 Git 部署至 Azure ](deploy-local-git.md)。 Git 型部署至 Azure 可啟用版本控制、封裝還原、MSBuild 等等。

## <a name="more-resources"></a>其他資源

* [Azure App Service 部署認證](deploy-configure-credentials.md)
