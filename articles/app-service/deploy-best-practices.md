---
title: 部署最佳做法
description: 瞭解部署到 Azure App Service 的主要機制。 尋找特定語言的建議和其他注意事項。
keywords: azure app service，web 應用程式，部署，部署，管線，組建
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 74bd7bc159f7f5974452adf6b2f51148d869b4ed
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589231"
---
# <a name="deployment-best-practices"></a>部署最佳作法

每個開發小組都有獨特的需求，可讓您在任何雲端服務上執行有效率的部署管線。 本文介紹部署至 App Service 的三個主要元件：部署來源、組建管線和部署機制。 本文也涵蓋特定語言堆疊的一些最佳作法和秘訣。

## <a name="deployment-components"></a>部署元件

### <a name="deployment-source"></a>部署來源

部署來源是您應用程式程式碼的位置。 針對生產應用程式，部署來源通常是由版本控制軟體（例如 [GitHub、BitBucket 或 Azure Repos](deploy-continuous-deployment.md)）所裝載的存放庫。 針對開發和測試案例，部署來源可能是 [您本機電腦上的專案](deploy-local-git.md)。 App Service 也支援 [OneDrive 和 Dropbox 資料夾](deploy-content-sync.md) 作為部署來源。 雖然雲端資料夾可讓您輕鬆地開始使用 App Service，但通常不建議將此來源用於企業層級的生產應用程式。 

### <a name="build-pipeline"></a>組建管線

一旦您決定部署來源之後，下一步就是選擇組建管線。 組建管線會從部署來源讀取您的原始程式碼，並執行一系列的步驟 (例如編譯器代碼、縮小 HTML 和 JavaScript、執行測試，以及封裝元件) ，讓應用程式處於可執行狀態。 組建管線所執行的特定命令取決於您的語言堆疊。 這些作業可以在組建伺服器上執行，例如 Azure Pipelines，或在本機執行。

### <a name="deployment-mechanism"></a>部署機制

部署機制是用來將您的內建應用程式放入 web 應用程式之 */home/site/wwwroot* 目錄的動作。 */Wwwroot* 目錄是由 web 應用程式的所有實例共用的已掛接儲存位置。 當部署機制將您的應用程式放在這個目錄時，您的實例會收到同步處理新檔案的通知。 App Service 支援下列部署機制：

- Kudu 端點： [Kudu](https://github.com/projectkudu/kudu/wiki) 是開放原始碼開發人員生產力工具，可在 Windows App Service 中作為個別的進程執行，並作為 Linux App Service 的第二個容器。 Kudu 會處理持續部署，並提供用於部署的 HTTP 端點，例如 zipdeploy。
- FTP 和 WebDeploy：使用您的 [網站或使用者認證](deploy-configure-credentials.md)，您可以透過 [FTP](deploy-ftp.md) 或 WebDeploy 上傳檔案。 這些機制不會經過 Kudu。  

Azure Pipelines、Jenkins 和編輯器外掛程式等部署工具會使用其中一種部署機制。

## <a name="use-deployment-slots"></a>使用部署位置

可能的話，請在部署新的生產組建時使用 [部署](deploy-staging-slots.md) 位置。 使用標準 App Service 方案層或更高時，您可以將應用程式部署至預備環境、驗證變更，以及執行冒煙測試。 當您準備好時，您可以交換預備和生產位置。 交換作業會會備妥必要的背景工作角色實例，以符合您的生產環境規模，從而消除停機時間。

### <a name="continuously-deploy-code"></a>持續部署程式碼

如果您的專案已指定用於測試、QA 和預備環境的分支，則應該將每個分支援續部署到預備位置。  (這就是所謂的 [>gitflow 設計](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)。 ) 這可讓您的專案關係人輕鬆地評估和測試已部署的分支。 

永遠不應針對您的生產位置啟用持續部署。 相反地，您的生產分支 (通常會將主要) 部署到非生產位置。 當您準備好釋出基底分支時，請將它交換到生產位置。 切換至生產環境（而不是部署到生產環境）可避免停機時間，並可讓您再次交換以復原變更。 

![此圖顯示開發、預備和主要分支之間的流程，以及部署至的位置。](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>持續部署容器

針對來自 Docker 或其他容器登錄的自訂容器，將映射部署到預備位置，並交換至生產環境以防止停機。 自動化比程式碼部署更為複雜，因為您必須將映射推送至容器登錄，並更新 webapp 上的映射標記。

針對您想要部署到位置的每個分支，設定自動化，以在每次認可至分支時執行下列動作。

1. **建立並標記映射**。 在組建管線中，使用 git 認可識別碼、時間戳記或其他可識別的資訊來標記映射。 最好不要使用預設的「最新」標記。 否則，很難追蹤目前部署的程式碼，這使得偵錯工具更難進行。
1. **推送標記的映射**。 一旦建立並標記映射，管線就會將映射推送至容器登錄。 在下一個步驟中，部署位置會從容器登錄中提取標記的映射。
1. **使用新的映射標記更新部署** 位置。 更新此屬性時，網站會自動重新開機並提取新的容器映射。

![插槽使用量視覺效果](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

常見的自動化架構有下列範例。

### <a name="use-azure-devops"></a>使用 Azure DevOps

App Service 透過部署中心 [內建容器的持續傳遞](deploy-continuous-deployment.md) 。 在 [Azure 入口網站](https://portal.azure.com/)中流覽至您的應用程式，然後選取 [**部署**] 底下的 [**部署中心**]。 請依照指示來選取您的儲存機制和分支。 這會設定 DevOps 組建和發行管線，以在新的認可推送至您選取的分支時，自動建立、標記及部署您的容器。

### <a name="use-github-actions"></a>使用 GitHub Actions

您也可以 [使用 GitHub Actions](deploy-container-github-action.md)自動化容器部署。  下列工作流程檔案將會使用認可識別碼來建立並標記容器、將其推送至容器登錄，以及使用新的映射標籤更新指定的網站位置。

```yaml
name: Build and deploy a container image to Azure Web Apps

on:
  push:
    branches:
    - <your-branch-name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@main

    -name: Authenticate using a Service Principal
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_SP }}

    - uses: azure/container-actions/docker-login@v1
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and push the image tagged with the git commit hash
      run: |
        docker build . -t contoso/demo:${{ github.sha }}
        docker push contoso/demo:${{ github.sha }}

    - name: Update image tag on the Azure Web App
      uses: azure/webapps-container-deploy@v1
      with:
        app-name: '<your-webapp-name>'
        slot-name: '<your-slot-name>'
        images: 'contoso/demo:${{ github.sha }}'
```

### <a name="use-other-automation-providers"></a>使用其他自動化提供者

先前所列的步驟適用于其他自動化公用程式，例如 CircleCI 或 Travis CI。 不過，您必須使用 Azure CLI，在最後一個步驟中使用新的映射標籤來更新部署位置。 若要在您的自動化腳本中使用 Azure CLI，請使用下列命令來產生服務主體。

```shell
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

在您的腳本中，使用來登入 `az login --service-principal` ，並提供主體的資訊。 然後，您可以使用 `az webapp config container set` 設定容器名稱、標記、登錄 URL 和登錄密碼。 以下是一些實用的連結，可讓您用來建立容器 CI 進程。

- [如何登入圓形 CI 上的 Azure CLI](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>Language-Specific 考慮

### <a name="java"></a>Java

使用 Kudu [zipdeploy/](deploy-zip.md) API 部署 JAR 應用程式，並針對 WAR 應用程式使用 [wardeploy/](deploy-zip.md#deploy-war-file) 。 如果您使用 Jenkins，您可以直接在部署階段使用這些 Api。 如需詳細資訊，請參閱[這篇文章](/azure/developer/jenkins/deploy-to-azure-app-service-using-azure-cli)。

### <a name="node"></a>節點

根據預設，Kudu 會執行節點應用程式 () 的組建步驟 `npm install` 。 如果您使用的是組建服務（例如 Azure DevOps），則 Kudu 組建是不必要的。 若要停用 Kudu 組建，請建立應用程式設定， `SCM_DO_BUILD_DURING_DEPLOYMENT` 並將值設定為 `false` 。

### <a name="net"></a>.NET 

根據預設，Kudu 會執行 .NET 應用程式 () 的組建步驟 `dotnet build` 。 如果您使用的是組建服務（例如 Azure DevOps），則 Kudu 組建是不必要的。 若要停用 Kudu 組建，請建立應用程式設定， `SCM_DO_BUILD_DURING_DEPLOYMENT` 並將值設定為 `false` 。

## <a name="other-deployment-considerations"></a>其他部署考慮

### <a name="local-cache"></a>本機快取

Azure App Service 的內容儲存在 Azure 儲存體中，並且會持久顯示為內容共用。 不過，有些應用程式只需要高效能、唯讀的內容存放區，才能以高可用性執行。 這些應用程式可利用 [本機](overview-local-cache.md)快取來獲益。 不建議將本機快取用於內容管理網站，例如 WordPress。

請一律使用本機快取搭配 [部署](deploy-staging-slots.md) 位置，以避免停機。 如需搭配使用這些功能的詳細資訊，請參閱 [這一節](overview-local-cache.md#best-practices-for-using-app-service-local-cache) 。

### <a name="high-cpu-or-memory"></a>高 CPU 或記憶體

如果您的 App Service 方案使用超過90% 的可用 CPU 或記憶體，則基礎虛擬機器可能會在處理您的部署時發生問題。 發生這種情況時，請暫時擴大您的實例計數，以執行部署。 部署完成後，您可以將實例計數傳回先前的值。

如需最佳做法的詳細資訊，請造訪 [App Service 診斷](./overview-diagnostics.md) ，以找出您的資源專屬的可行最佳作法。

- 在 [Azure 入口網站](https://portal.azure.com)中流覽至您的 Web 應用程式。
- 在左側導覽中，按一下 [ **診斷並解決問題** ]，即可開啟 App Service 診斷。
- 選擇 **最佳作法** 首頁圖格。
- 按一下 [ **可用性] 的最佳作法 & 效能** 或 **最佳做法，以最佳** 設定來查看應用程式目前的狀態，以瞭解這些最佳做法。

您也可以使用此連結來直接開啟資源的 App Service 診斷： `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot` 。