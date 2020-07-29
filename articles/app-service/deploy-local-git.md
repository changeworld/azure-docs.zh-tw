---
title: 從本機 Git 存放庫部署
description: 了解如何啟用本機 Git 部署至 Azure App Service。 從本機電腦部署程式碼最簡單的方式之一。
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 06/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: efe4c07a6231e0b2c95b049db056a4e5d055db98
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77152987"
---
# <a name="local-git-deployment-to-azure-app-service"></a>本機 Git 部署至 Azure App Service

本操作指南會示範如何將您的應用程式部署至本機電腦上的 Git 儲存機制[Azure App Service](overview.md) 。

## <a name="prerequisites"></a>必要條件

遵循本使用說明指南中的步驟：

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [安裝 Git](https://www.git-scm.com/downloads)。
  
- 具有您想要部署之程式碼的本機 Git 存放庫。 若要下載範例存放庫，請在您的本機終端機視窗中執行下列命令：
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>使用 Kudu 組建伺服器進行部署

若要使用 Kudu App Service 組建伺服器為您的應用程式啟用本機 Git 部署，最簡單的方式是使用 Azure Cloud Shell。 

### <a name="configure-a-deployment-user"></a>設定部署使用者

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>取得部署 URL

若要取得 URL 以啟用現有應用程式的本機 Git 部署，請 [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) 在 Cloud Shell 中執行。 將 \<app-name> 和取代為 \<group-name> 您的應用程式和其 Azure 資源群組的名稱。

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```
> [!NOTE]
> 如果您使用 linux 應用程式服務方案，則需要新增此參數：--runtime python | 3。7


或者，若要建立啟用 Git 的新應用程式，請 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) 使用參數在 Cloud Shell 中執行 `--deployment-local-git` 。 \<app-name> \<group-name> \<plan-name> 以新的 Git 應用程式、其 Azure 資源群組及其 Azure App Service 方案的名稱取代、和。

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

其中一個命令會傳回如下的 URL： `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` 。 在下一個步驟中，請使用此 URL 來部署您的應用程式。

您也可以使用應用層級認證來啟用本機 Git，而不是使用此帳戶層級的 URL。 Azure App Service 會自動為每個應用程式產生這些認證。 

在 Cloud Shell 中執行下列命令，以取得應用程式認證。 \<app-name>將和取代 \<group-name> 為您的應用程式名稱和 Azure 資源組名。

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

使用在下一個步驟中傳回的 URL 來部署您的應用程式。

### <a name="deploy-the-web-app"></a>部署 Web 應用程式

1. 將本機的終端機視窗開啟至您的本機 Git 存放庫，並新增 Azure 遠端。 在下列命令中， \<url> 將取代為您在上一個步驟中所獲得的部署使用者特定 url 或應用程式特定的 url。
   
   ```bash
   git remote add azure <url>
   ```
   
1. 使用推送至 Azure 遠端 `git push azure master` 。 
   
1. 在 [ **Git 認證管理員**] 視窗中，輸入您的[部署使用者密碼](#configure-a-deployment-user)，而不是您的 Azure 登入密碼。
   
1. 檢閱輸出。 您可能會看到執行時間特定的自動化，例如適用于 ASP.NET 的 MSBuild、 `npm install` for Node.js，以及 `pip install` 適用于 Python 的。 
   
1. 在 Azure 入口網站中流覽至您的應用程式，以確認內容已部署。

## <a name="deploy-with-azure-pipelines-builds"></a>使用 Azure Pipelines 組建部署

如果您的帳戶具有必要的許可權，您可以設定 Azure Pipelines （預覽），為您的應用程式啟用本機 Git 部署。 

- 您的 Azure 帳戶必須具有寫入 Azure Active Directory 和建立服務的許可權。 
  
- 您的 Azure 帳戶必須具有 Azure 訂用帳戶中的**擁有**者角色。

- 您必須是您想要使用之 Azure DevOps 專案中的系統管理員。

若要使用 Azure Pipelines （預覽）為您的應用程式啟用本機 Git 部署：

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [**應用程式服務**]。 

1. 選取您的 Azure App Service 應用程式，然後選取左側功能表中的 [**部署中心**]。
   
1. 在 [**部署中心**] 頁面上，選取 [**本機 Git**]，然後選取 [**繼續**]。 
   
   ![選取 [本機 Git]，然後選取 [繼續]](media/app-service-deploy-local-git/portal-enable.png)
   
1. 在 [**組建提供者**] 頁面上，選取 [ **Azure Pipelines （預覽）**]，然後選取 [**繼續**]。 
   
   ![選取 [Azure Pipelines （預覽）]，然後選取 [繼續]。](media/app-service-deploy-local-git/pipeline-builds.png)

1. 在 [**設定**] 頁面上，設定新的 Azure DevOps 組織，或指定現有的組織，然後選取 [**繼續**]。
   
   > [!NOTE]
   > 如果您現有的 Azure DevOps 組織未列出，您可能需要將它連結到您的 Azure 訂用帳戶。 如需詳細資訊，請參閱[定義您的 CD 發行管線](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)。
   
1. 視您的 App Service 方案[定價層](https://azure.microsoft.com/pricing/details/app-service/plans/)而定，您可能會看到 [**部署至預備**環境] 頁面。 選擇是否要[啟用部署](deploy-staging-slots.md)位置，然後選取 [**繼續**]。
   
1. 在 [**摘要**] 頁面上，檢查設定，然後選取 **[完成]**。
   
1. 當 Azure 管線備妥時，從 [**部署中心**] 頁面複製 [Git 存放庫 URL]，以在下一個步驟中使用。 
   
   ![複製 Git 存放庫 URL](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. 在您的本機終端機視窗中，將 Azure 遠端新增至您的本機 Git 存放庫。 在命令中，將取代 \<url> 為您在上一個步驟中所獲得的 Git 儲存機制 URL。
   
   ```bash
   git remote add azure <url>
   ```
   
1. 使用推送至 Azure 遠端 `git push azure master` 。 
   
1. 在 [ **Git 認證管理員**] 頁面上，使用您的 visualstudio.com 使用者名稱登入。 如需其他驗證方法，請參閱[Azure DevOps Services authentication 總覽](/vsts/git/auth-overview?view=vsts)。
   
1. 部署完成之後，請在上查看組建進度 `https://<azure_devops_account>.visualstudio.com/<project_name>/_build` ，並在部署進度 `https://<azure_devops_account>.visualstudio.com/<project_name>/_release` 。
   
1. 在 Azure 入口網站中流覽至您的應用程式，以確認內容已部署。

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>針對部署進行疑難排解

當您使用 Git 來發佈至 Azure 中的 App Service 應用程式時，您可能會看到下列常見的錯誤訊息：

|訊息|原因|解決方案
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|應用程式未啟動且正在執行。|在 Azure 入口網站中啟動應用程式。 當 web 應用程式停止時，無法使用 Git 部署。|
|`Couldn't resolve host 'hostname'`|' Azure ' 遠端的位址資訊不正確。|使用 `git remote -v` 命令，列出所有遠端以及相關聯的 URL。 驗證 'azure' 遠端的 URL 是否正確。 如有需要，移除此遠端並使用正確的 URL 重新建立。|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|您在期間未指定分支 `git push` ，或未 `push.default` 在中設定此值 `.gitconfig` 。|`git push`再次執行，並指定主要分支： `git push azure master` 。|
|`src refspec [branchname] does not match any.`|您嘗試在 ' azure ' 遠端上推送至 master 以外的分支。|`git push`再次執行，並指定主要分支： `git push azure master` 。|
|`RPC failed; result=22, HTTP code = 5xx.`|如果您嘗試透過 HTTPS 推送大型 Git 存放庫，就會發生這個錯誤。|變更本機電腦上的 git 設定，使其 `postBuffer` 變得更大。 例如： `git config --global http.postBuffer 524288000` 。|
|`Error - Changes committed to remote repository but your web app not updated.`|您已使用_package.json_檔案來部署 Node.js 應用程式，以指定其他必要的模組。|如需 `npm ERR!` 失敗的詳細內容，請參閱此錯誤之前的錯誤訊息。 以下是此錯誤的已知原因，以及對應的 `npm ERR!` 訊息：<br /><br />檔案**上格式不正確的 package.js**：`npm ERR! Couldn't read dependencies.`<br /><br />**原生模組沒有適用于 Windows 的二進位散發**：<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />或 <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>其他資源

- [專案 Kudu 文件](https://github.com/projectkudu/kudu/wiki)
- [持續部署至 Azure App Service](deploy-continuous-deployment.md)
- [範例：建立 web 應用程式並從本機 Git 存放庫部署程式碼（Azure CLI）](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [範例：建立 web 應用程式並從本機 Git 存放庫部署程式碼（PowerShell）](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
