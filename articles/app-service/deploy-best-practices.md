---
title: 部署最佳做法
description: 瞭解部署到 Azure 應用服務的關鍵機制。 查找特定於語言的建議和其他注意事項。
keywords: Azure 應用服務、Web 應用、部署、部署、管道、生成
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 4dd959d75fd582d787e68db4a415a4a694b9cda8
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770671"
---
# <a name="deployment-best-practices"></a>部署最佳實務

每個開發團隊都有獨特的要求,使在任何雲服務上實現高效的部署管道都很困難。 本文介紹了部署到應用服務的三個主要元件:部署源、生成管道和部署機制。 本文還介紹了特定語言堆疊的一些最佳實踐和提示。

## <a name="deployment-components"></a>部署元件

### <a name="deployment-source"></a>部署來源

部署源是應用程式代碼的位置。 對於生產應用,部署源通常是由版本控制軟體(如[GitHub、BitBucket 或 Azure 存儲庫](deploy-continuous-deployment.md))託管的儲存庫。 對開發與測試機制,部署來源可能是[本地電腦上的專案](deploy-local-git.md)。 應用服務還支援[OneDrive 和 Dropbox 資料夾](deploy-content-sync.md)作為部署源。 雖然雲資料夾可以方便地開始使用應用服務,但通常不建議將此源用於企業級生產應用程式。 

### <a name="build-pipeline"></a>組建管線

確定部署源後,下一步是選擇生成管道。 生成管道從部署源讀取原始程式碼,並執行一系列步驟(如編譯代碼、對 HTML 和 JAvaScript 進行簡單化、運行測試和打包元件),以使應用程式處於可執行狀態。 生成管道執行的特定命令取決於您的語言堆疊。 這些操作可以在生成伺服器上執行(如 Azure 管道),也可以在本地執行。

### <a name="deployment-mechanism"></a>部署機制

部署機制是用於將內建應用程式放入 Web 應用的 */home/site/wwwroot*目錄中的操作。 */wwwroot*目錄是 Web 應用的所有實體共用的裝載存儲位置。 當部署機制將應用程式放入此目錄中時,實例會收到同步新檔的通知。 套用服務支援以下部署機制:

- Kudu 終結點[:Kudu](https://github.com/projectkudu/kudu/wiki)是開源開發人員生產力工具,在 Windows 應用服務中作為單獨的進程運行,並作為 Linux 應用服務中的第二個容器運行。 庫杜處理連續部署並提供用於部署的 HTTP 終結點,如 zipdeploy。
- FTP 和 WebDeploy:使用[您的網站或使用者認證](deploy-configure-credentials.md),您可以透過[FTP](deploy-ftp.md)或 WebDeploy 上傳檔。 這些機制不通過庫杜。  

部署工具(如 Azure 管道、Jenkins 和編輯器外掛程式)使用這些部署機制之一。

## <a name="use-deployment-slots"></a>使用部署槽

讓您可以在部署新的生產產生時使用[部署槽](deploy-staging-slots.md)。 使用標準應用服務計畫層或更高級別時,可以將應用部署到暫存環境、驗證更改並執行煙霧測試。 準備就緒后,可以交換暫存和生產槽。 交換操作預熱必要的輔助角色實例,以匹配您的生產規模,從而消除停機時間。

### <a name="continuously-deploy-code"></a>持續部署代碼

如果專案具有用於測試、QA 和暫存的指定分支,則應將每個分支持續部署到暫存槽。 (這稱為[Gitflow 設計](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)。這使您的利益干系人可以輕鬆地評估和測試已部署的分支。 

絕不應為生產槽啟用連續部署。 相反,生產分支(通常是主分支)應部署到非生產槽。 準備好釋放基礎分支時,將其交換到生產槽中。 交換到生產中(而不是部署到生產中)可以防止停機,並允許您通過再次交換回滾更改。 

![插槽使用視覺化](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>持續部署容器

對於來自 Docker 或其他容器註冊表的自定義容器,請將映射部署到暫存槽並交換到生產中,以防止停機。 自動化比代碼部署更複雜,因為必須將映射推送到容器註冊表並更新 Webapp 上的映像標記。

對於要部署到槽的每個分支,設置自動化以對每個提交分支執行以下操作。

1. **產生與標籤影像**。 作為生成管道的一部分,使用 git 提交 ID、時間戳或其他可識別資訊標記映射。 最好不要使用預設的" 否則,很難追溯當前部署的代碼,這使得調試更加困難。
1. **按下標記的影像**。 生成和標記映射後,管道將映射推送到我們的容器註冊表。 在下一步中,部署槽將從容器註冊錶中提取標記的圖像。
1. **使用新的映像標記更新部署槽**。 更新此屬性時,網站將自動重新啟動並拉取新的容器映射。

![插槽使用視覺化](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

以下是常見自動化框架的範例。

### <a name="use-azure-devops"></a>使用 Azure DevOps

套用服務已[透過部署中心為容器提供內建連續交付](deploy-continuous-deployment.md)。 在[Azure 門戶](https://portal.azure.com/)中瀏覽到你的應用程式,並在**部署**下選擇**部署中心**。 按照說明選擇存儲庫和分支。 這將配置 DevOps 生成和發佈管道,以便在新提交推送到所選分支時自動生成、標記和部署容器。

### <a name="use-github-actions"></a>使用 GitHub 作業

您還可以[使用 GitHub 操作](containers/deploy-container-github-action.md)自動執行容器部署。  下面的工作流檔將生成和使用提交 ID 標記容器,將其推送到容器註冊表,以及使用新的映射標記更新指定的網站槽。

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
    - uses: actions/checkout@master

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

前面列出的步驟適用於其他自動化實用程式,如CircleCI或Travis CI。 但是,您需要使用 Azure CLI 在最後一步中使用新的映射標記更新部署槽。 要在自動化文稿中使用 Azure CLI,請使用以下命令生成服務主體。

```shell
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

在腳本中,使用登錄`az login --service-principal`,提供主體的資訊。 然後,可以使用`az webapp config container set`設置容器名稱、標記、註冊表 URL 和註冊表密碼。 下面是一些有用的連結,用於構建容器 CI 進程。

- [如何在圓 CI 上登入 Azure CLI](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>特定於語言的注意事項

### <a name="java"></a>Java

使用 Kudu [zipdeploy/API](deploy-zip.md)部署 JAR 應用程式,以及 WAR 應用程式[的戰爭部署/](deploy-zip.md#deploy-war-file) 如果使用 Jenkins,則可以在部署階段直接使用這些 API。 如需詳細資訊，請參閱 [本篇文章](../jenkins/execute-cli-jenkins-pipeline.md)。

### <a name="node"></a>節點

默認情況下,Kudu 執行 Node`npm install`應用程式 () 的生成步驟。 如果使用生成服務(如 Azure DevOps),則不需要 Kudu 生成。 要關閉 Kudu 產生,請建立一`SCM_DO_BUILD_DURING_DEPLOYMENT`個 應用程式設定,`false`其值為 。

### <a name="net"></a>.NET 

預設情況下,Kudu 執行 .NET 應用程式的產生`dotnet build`步驟 ( 。 如果使用生成服務(如 Azure DevOps),則不需要 Kudu 生成。 要關閉 Kudu 產生,請建立一`SCM_DO_BUILD_DURING_DEPLOYMENT`個 應用程式設定,`false`其值為 。

## <a name="other-deployment-considerations"></a>其他部署注意事項

### <a name="local-cache"></a>本機快取

Azure App Service 的內容儲存在 Azure 儲存體中，並且會持久顯示為內容共用。 但是,某些應用只需要一個高性能的、只讀的內容存儲,它們就可以以高可用性運行。 這些應用程式可以從使用[本地緩存](overview-local-cache.md)中獲益。 不建議對內容管理網站(如 WordPress)進行本地緩存。

始終將本地緩存與[部署插槽](deploy-staging-slots.md)結合使用,以防止停機。 有關將這些功能結合使用的資訊,請參閱[本節](overview-local-cache.md#best-practices-for-using-app-service-local-cache)。

### <a name="high-cpu-or-memory"></a>高 CPU 或記憶體

如果應用服務計劃使用了超過 90% 的可用 CPU 或記憶體,則基礎虛擬機器在處理部署時可能遇到問題。 發生這種情況時,請臨時向上擴展實例計數以執行部署。 部署完成後,可以將實例計數返回到其上一個值。

有關最佳實踐的詳細資訊,請造[訪 應用服務診斷](https://docs.microsoft.com/azure/app-service/overview-diagnostics),瞭解特定於您的資源的可操作最佳做法。

- 在[Azure 門戶](https://portal.azure.com)中導航到 Web 應用。
- 單擊 **「診斷並解決**左側導航中的問題」,該導航將打開應用服務診斷。
- 選擇**最佳實踐**主頁磁貼。
- 按下 **「最佳**設定&可用性的最佳做法」或 **「最佳配置的最佳做法**」,查看應用對這些最佳實務的當前狀態。

您可以使用此連結直接開啟資源的應用程式服務診斷: `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`。
