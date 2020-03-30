---
title: 從本機 Git 存放庫部署
description: 了解如何啟用本機 Git 部署至 Azure App Service。 從本地電腦部署代碼的最簡單方法之一。
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 06/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: efe4c07a6231e0b2c95b049db056a4e5d055db98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152987"
---
# <a name="local-git-deployment-to-azure-app-service"></a>本地 Git 部署到 Azure 應用服務

本操作指南演示如何從本地電腦上的 Git 存儲庫將應用部署到[Azure 應用服務](overview.md)。

## <a name="prerequisites"></a>Prerequisites

遵循本使用說明指南中的步驟：

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [安裝 Git](https://www.git-scm.com/downloads)。
  
- 擁有本地 Git 存儲庫，其中帶有要部署的代碼。 要下載示例存儲庫，請在本地終端視窗中運行以下命令：
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>使用庫杜生成伺服器進行部署

使用 Kudu 應用服務生成伺服器為應用啟用本地 Git 部署的最簡單方法是使用 Azure 雲外殼。 

### <a name="configure-a-deployment-user"></a>設定部署使用者

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>獲取部署 URL

要獲取 URL 以為現有應用啟用本地 Git 部署[`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git)，在雲外殼中運行。 將\<應用名稱>和\<組名>替換為應用及其 Azure 資源組的名稱。

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```
> [!NOTE]
> 如果使用 linux 應用服務方案，則需要添加此參數： --運行時 python_3.7


或者，要創建新啟用 Git 的應用，請使用[`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)參數`--deployment-local-git`在雲外殼中運行。 將\<應用名稱>、\<組名>和\<計畫名稱>替換為新 Git 應用、其 Azure 資源組及其 Azure 應用服務方案的名稱。

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

任一命令返回一個`https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`URL，如下所示： 。 使用此 URL 在下一步中部署應用。

您還可以使用應用級憑據啟用本地 Git，而不是使用此帳戶級 URL。 Azure 應用服務會自動為每個應用生成這些憑據。 

通過在雲外殼中運行以下命令來獲取應用憑據。 將\<應用名稱>和\<組名>替換為應用的名稱和 Azure 資源組名稱。

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

使用返回的 URL 在下一步中部署應用。

### <a name="deploy-the-web-app"></a>部署 Web 應用程式

1. 打開本地 Git 存儲庫的本地終端視窗，然後添加 Azure 遙控器。 在以下命令中，將\<url>替換為從上一步驟中獲得的部署使用者特定 URL 或應用特定 URL。
   
   ```bash
   git remote add azure <url>
   ```
   
1. 使用`git push azure master`推送到 Azure 遠端。 
   
1. 在**Git 認證管理員**視窗中，輸入[部署使用者密碼](#configure-a-deployment-user)，而不是 Azure 登錄密碼。
   
1. 檢閱輸出。 您可能會看到特定于運行時的自動化，例如 ASP.NET的 MSBuild、Node.js`npm install`和`pip install`Python。 
   
1. 流覽到 Azure 門戶中的應用，以驗證內容是否已部署。

## <a name="deploy-with-azure-pipelines-builds"></a>使用 Azure 管道生成進行部署

如果帳戶具有必要的許可權，則可以設置 Azure 管道（預覽）以為應用啟用本地 Git 部署。 

- Azure 帳戶必須具有寫入 Azure 活動目錄和創建服務的許可權。 
  
- Azure 帳戶必須在 Azure 訂閱中具有**擁有者**角色。

- 您必須是要使用的 Azure DevOps 專案中的管理員。

要使用 Azure 管道為應用啟用本地 Git 部署（預覽）：

1. 在[Azure 門戶](https://portal.azure.com)中，搜索並選擇**應用服務**。 

1. 選擇 Azure 應用服務應用，並在左側功能表中選擇 **"部署中心**"。
   
1. 在 **"部署中心**"頁上，選擇 **"本地 Git"，** 然後選擇"**繼續**"。 
   
   ![選擇本地 Git，然後選擇"繼續"](media/app-service-deploy-local-git/portal-enable.png)
   
1. 在 **"生成提供程式"** 頁上，選擇**Azure 管道（預覽），** 然後選擇"**繼續**"。 
   
   ![選擇 Azure 管道（預覽），然後選擇"繼續"。](media/app-service-deploy-local-git/pipeline-builds.png)

1. 在 **"配置"** 頁上，配置新的 Azure DevOps 組織，或指定現有組織，然後選擇"**繼續**"。
   
   > [!NOTE]
   > 如果未列出現有的 Azure DevOps 組織，則可能需要將其連結到 Azure 訂閱。 有關詳細資訊，請參閱定義[CD 發行管道](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)。
   
1. 根據您的應用服務方案[定價層](https://azure.microsoft.com/pricing/details/app-service/plans/)，您可能會看到 **"部署到暫存"** 頁。 選擇是否[啟用部署槽](deploy-staging-slots.md)，然後選擇"**繼續**"。
   
1. 在 **"摘要"** 頁上，查看設置，然後選擇 **"完成**"。
   
1. Azure 管道準備就緒後，從**部署中心**頁面複製 Git 存儲庫 URL，以便在下一步中使用。 
   
   ![複製 Git 存儲庫 URL](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. 在本地終端視窗中，將 Azure 遠端添加到本地 Git 存儲庫。 在命令中，將\<url>替換為從上一步驟中得到的 Git 存儲庫的 URL。
   
   ```bash
   git remote add azure <url>
   ```
   
1. 使用`git push azure master`推送到 Azure 遠端。 
   
1. 在**Git 認證管理員**頁面上，使用visualstudio.com使用者名登錄。 有關其他身份驗證方法，請參閱[Azure DevOps 服務身份驗證概述](/vsts/git/auth-overview?view=vsts)。
   
1. 部署完成後，在 上`https://<azure_devops_account>.visualstudio.com/<project_name>/_build`查看生成進度，並在 上`https://<azure_devops_account>.visualstudio.com/<project_name>/_release`查看部署進度。
   
1. 流覽到 Azure 門戶中的應用，以驗證內容是否已部署。

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>針對部署進行疑難排解

當您使用 Git 發佈到 Azure 中的應用服務應用時，您可能會看到以下常見錯誤訊息：

|訊息|原因|解決方案
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|應用程式未啟動並運行。|在 Azure 入口網站中啟動應用程式。 停止 Web 應用時，Git 部署不可用。|
|`Couldn't resolve host 'hostname'`|"azure"遠端的位址資訊不正確。|使用 `git remote -v` 命令，列出所有遠端以及相關聯的 URL。 驗證 'azure' 遠端的 URL 是否正確。 如有需要，移除此遠端並使用正確的 URL 重新建立。|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|在 期間`git push`未指定分支，或者未在 中`push.default``.gitconfig`設置 該值。|再次`git push`運行，指定主分支： `git push azure master`。|
|`src refspec [branchname] does not match any.`|您嘗試推送到"azure"遙控器上的主控分支以外的分支。|再次`git push`運行，指定主分支： `git push azure master`。|
|`RPC failed; result=22, HTTP code = 5xx.`|如果您嘗試透過 HTTPS 推送大型 Git 存放庫，就會發生這個錯誤。|更改本地電腦上的 git 配置以`postBuffer`變大。 例如：`git config --global http.postBuffer 524288000`。|
|`Error - Changes committed to remote repository but your web app not updated.`|使用指定其他所需模組的_包.json_檔部署了 Node.js 應用。|查看此`npm ERR!`錯誤之前的錯誤訊息，瞭解有關故障的更多上下文。 以下是此錯誤的已知原因以及相應的`npm ERR!`消息：<br /><br />**格式錯誤的包.json 檔**：`npm ERR! Couldn't read dependencies.`<br /><br />**本機模組沒有 Windows 的二進位分發**：<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />或 <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>其他資源

- [專案 Kudu 文件](https://github.com/projectkudu/kudu/wiki)
- [持續部署到 Azure 應用服務](deploy-continuous-deployment.md)
- [示例：創建 Web 應用並從本地 Git 存儲庫 （Azure CLI） 部署代碼](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [示例：創建 Web 應用並從本地 Git 存儲庫 （PowerShell） 部署代碼](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
