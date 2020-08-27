---
title: 使用 ZIP 或 WAR 檔案來部署程式碼
description: 了解如何使用 ZIP 檔案 (若為 Java 開發人員，則是 WAR 檔案) 將您的應用程式部署至 Azure App Service。
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18
ms.openlocfilehash: e2959403d2e5db38d03013e798fe299d56837227
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962106"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>使用 ZIP 或 WAR 檔案將您的應用程式部署至 Azure App Service

本文說明如何使用 ZIP 檔案或 WAR 檔案將您的 Web 應用程式部署到 [Azure App Service](overview.md)。 

此 ZIP 檔案部署所使用的是支援持續整合式部署的同一個 Kudu 服務。 Kudu 為 ZIP 檔案部署支援下列功能： 

- 刪除先前部署所留下的檔案。
- 用來開啟預設建置程序的選項，其中包含套件還原。
- 部署自訂，包括執行中的部署指令碼。  
- 部署記錄。 
- 檔案大小限制為 2048 MB。

如需詳細資訊，請參閱 [Kudu 文件](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)。

WAR 檔案部署會將您的 [WAR](https://wikipedia.org/wiki/WAR_(file_format)) 檔案部署至 App Service，以執行您的 Java Web 應用程式。 請參閱[部署 WAR 檔案](#deploy-war-file)。

> [!NOTE]
> 使用時 `ZipDeploy` ，只有當檔案的時間戳記不符合已部署的檔案時，才會複製檔案。 使用可快取輸出的組建進程產生 zip，可能會導致更快速的部署。 如需詳細資訊，請參閱 [從 zip 檔案或 url 進行部署](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)。

## <a name="prerequisites"></a>Prerequisites

若要完成本文中的步驟，請 [建立 App Service 應用程式](./index.yml)，或使用您為另一個教學課程所建立的應用程式。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
上述端點目前無法在 Linux 應用程式服務上運作。 請考慮改為使用 FTP 或 [ZIP DEPLOY API](faq-app-service-linux.md#continuous-integration-and-deployment) 。

## <a name="deploy-zip-file-with-azure-cli"></a>使用 Azure CLI 部署 ZIP 檔案

使用 [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) 命令，將上傳的 ZIP 檔案部署至 Web 應用程式。  

下列範例會部署您上傳的 ZIP 檔案。 使用 Azure CLI 的本機安裝時，請針對 `--src` 指定您的本機 ZIP 檔案路徑。

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

此命令會將檔案和目錄從 ZIP 檔案部署到預設的 App Service 應用程式資料夾 (`\home\site\wwwroot`)，並重新啟動應用程式。

根據預設，部署引擎會假設 ZIP 檔案已就緒可依原樣執行，且不會執行任何組建自動化。 若要啟用與 [Git 部署](deploy-local-git.md)相同的組建自動化，請 `SCM_DO_BUILD_DURING_DEPLOYMENT` 在 [Cloud Shell](https://shell.azure.com)中執行下列命令來設定應用程式設定：

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

如需詳細資訊，請參閱 [Kudu 文件](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)。

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>部署 WAR 檔案

若要將 WAR 檔案部署至 App Service，請將 POST 要求傳送至 `https://<app-name>.scm.azurewebsites.net/api/wardeploy` 。 POST 要求必須在訊息本文中包含 .war 檔案。 系統會使用 HTTP 基本驗證，在要求中提供應用程式的部署認證。

`/api/wardeploy`部署 WAR 檔案時一律使用。 此 API 會擴充您的 WAR 檔案，並將它放在共用的檔案磁片磁碟機上。 使用其他部署 Api 可能會導致不一致的行為。 

針對 HTTP 基本驗證，您需要 App Service 部署的認證。 若要了解如何設定部署認證，請參閱[設定及重設使用者層級的認證](deploy-configure-credentials.md#userscope)。

### <a name="with-curl"></a>使用 cURL

下列範例會使用 cURL 工具來部署 .war 檔案。 取代預留位置 `<username>`、`<war-file-path>` 和 `<app-name>`。 當 cURL 顯示提示時，請輸入密碼。

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>透過 PowerShell

下列範例會使用 [set-azwebapp](/powershell/module/az.websites/publish-azwebapp) 上傳 war 檔。 取代預留位置 `<group-name>`、`<app-name>` 和 `<war-file-path>`。

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>後續步驟

如需更多的進階部署案例，請嘗試[使用 Git 部署至 Azure ](deploy-local-git.md)。 Git 型部署至 Azure 可啟用版本控制、封裝還原、MSBuild 等等。

## <a name="more-resources"></a>其他資源

* [Kudu：從 zip 檔案部署](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Azure App Service 部署認證](deploy-ftp.md)