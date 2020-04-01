---
title: 設定連續部署
description: 瞭解如何從 GitHub、Bit Bucket、Azure 儲存庫或其他儲存庫將 CI/CD 啟用 Azure 應用服務。 選擇適合您需求的生成管道。
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/20/2020
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 847de2c2c8916558d542473d9b7c80fd5552dbf7
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437139"
---
# <a name="continuous-deployment-to-azure-app-service"></a>持續部署至 Azure App Service

[Azure 應用服務](overview.md)通過提取最新更新,支援從 GitHub、Bit Bucket 和[Azure 存儲庫](https://azure.microsoft.com/services/devops/repos/)存儲庫進行連續部署。 本文介紹如何使用 Azure 門戶通過 Kudu 生成服務或[Azure 管道](https://azure.microsoft.com/services/devops/pipelines/)持續部署應用。 

有關原始碼管理服務的詳細資訊,請參閱[創建儲存庫 (GitHub)、][創建儲存庫(Bit Bucket)]或[創建新的 Git 儲存庫(Azure 儲存庫)。]

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>授權 Azure 應用服務 

要使用 Azure 儲存庫,請確保 Azure DevOps 組織已連結到 Azure 訂閱。 有關詳細資訊,請參閱設定[Azure DevOps 服務帳戶,以便它可以部署到 Web 應用](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)。

對於 Bitbucket 或 GitHub,授權 Azure 應用服務連接到儲存庫。 您只需使用原始程式碼管理服務授權一次。 

1. 在[Azure 門戶](https://portal.azure.com)中,搜索**應用服務**並選擇。

   ![搜索應用服務。](media/app-service-continuous-deployment/search-for-app-services.png)

1. 選擇要部署的應用服務。

   ![選擇你的應用。](media/app-service-continuous-deployment/select-your-app.png)
   
1. 在應用頁面上,選擇左側功能表中的 **「部署中心**」。
   
1. 在 **「部署中心**」頁上,選擇**GitHub**或**Bitbucket,** 然後選擇 **「授權**」 。。 
   
   ![選擇原始程式碼管理服務,然後選擇"授權"](media/app-service-continuous-deployment/github-choose-source.png)
   
1. 如有必要,登錄到服務,然後按照授權提示進行操作。 

## <a name="enable-continuous-deployment"></a>啟用持續部署 

授權原始程式碼管理服務後,請配置應用,以便透過內建[Kudu 應用服務](#option-1-kudu-app-service)生成伺服器或透過[Azure 管道](#option-2-azure-pipelines)進行持續部署。 

### <a name="option-1-kudu-app-service"></a>選項 1:函式庫杜應用服務

您可以使用內建庫杜應用服務生成伺服器從 GitHub、Bitbucket 或 Azure 儲存庫持續部署。 

1. 在[Azure 門戶](https://portal.azure.com)中,搜索**應用服務**,然後選擇要部署的應用服務。 
   
1. 在應用頁面上,選擇左側功能表中的 **「部署中心**」。
   
1. 在 **「部署中心**」頁上選擇您的授權原始程式碼管理提供程式,然後選擇「**繼續**」。 對於 GitHub 或 Bitbucket,您還可以選擇 **「更改帳戶」** 以更改授權帳戶。 
   
   > [!NOTE]
   > 要使用 Azure 儲存庫,請確保 Azure DevOps 服務組織已連結到 Azure 訂閱。 有關詳細資訊,請參閱設定[Azure DevOps 服務帳戶,以便它可以部署到 Web 應用](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)。
   
1. 對於 GitHub 或 Azure 儲存庫,在**生成提供程式**頁上,選擇**應用服務生成服務**,然後選擇"**繼續**"。 Bitbucket 始終使用應用服務生成服務。
   
   ![選擇應用服務生成服務,然後選擇"繼續"。](media/app-service-continuous-deployment/choose-kudu.png)
   
1. 在 **「設定」** 頁上:
   
   - 對於 GitHub,下拉並選擇要連續部署**的組織**、**儲存庫**和**分支**。
     
     > [!NOTE]
     > 如果看不到任何存儲庫,則可能需要在 GitHub 中授權 Azure 應用服務。 瀏覽到您的 GitHub 儲存函式庫,然後轉到**設定** > **應用程式** > **授權 OAuth 應用程式**。 選擇**Azure 應用服務**,然後選擇 **"授予**"。 對於組織存儲庫,您必須是組織的擁有者才能授予許可權。
     
   - 對 Bitbucket,選擇要連續部署的 Bitbucket**團隊**,**儲存函式庫**與**分支**。
     
   - 對於 Azure 儲存函式庫,選擇要連續部署的**Azure DevOps 組織**,**專案**,**儲存函式庫**與**分支**。
     
     > [!NOTE]
     > 如果未列出 Azure DevOps 組織,請確保它連結到 Azure 訂閱。 有關詳細資訊,請參閱設定[Azure DevOps 服務帳戶,以便它可以部署到 Web 應用](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)。
     
1. 選取 **[繼續]**。
   
   ![填寫存儲庫資訊,然後選擇"繼續"。](media/app-service-continuous-deployment/configure-kudu.png)
   
1. 配置生成提供程式後,請查看 **「摘要」** 頁上的設置,然後選擇 **「完成**」 。
   
1. 所選存儲庫和分支中的新提交現在連續部署到應用服務應用中。 您可以在 **「部署中心**」頁上追蹤提交和部署。
   
   ![追蹤部署中心的提交並部署](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-azure-pipelines"></a>選項 2:Azure 管道 

如果帳戶具有必要的許可權,則可以設置 Azure 管道以從 GitHub 或 Azure 存儲庫持續部署。 有關透過 Azure 管道進行部署的詳細資訊,請參閱[將 Web 應用部署到 Azure 應用服務](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)。

#### <a name="prerequisites"></a>Prerequisites

對於使用 Azure 管道建立連續傳遞的 Azure 應用服務,Azure DevOps 組織應具有以下許可權: 

- Azure 帳戶必須具有寫入 Azure 活動目錄和創建服務的許可權。 
  
- Azure 帳戶必須在 Azure 訂閱中具有**擁有者**角色。

- 您必須是要使用的 Azure DevOps 專案中的管理員。

#### <a name="github--azure-pipelines"></a>GitHub + Azure 管道

1. 在[Azure 門戶](https://portal.azure.com)中,搜索**應用服務**,然後選擇要部署的應用服務。 
   
1. 在應用頁面上,選擇左側功能表中的 **「部署中心**」。

1. 在**部署中心**頁面上選擇**GitHub**作為原始碼管理提供程式,然後選擇「**繼續**」。 對於**GitHub,** 您可以選擇 **「更改帳戶」** 來更改授權帳戶。

    ![原始檔控制](media/app-service-continuous-deployment/deployment-center-src-control.png)
   
1. 產生**提供程式**頁上,選擇**Azure 管道(預覽),** 然後選擇「**繼續**」。

    ![產生提供者](media/app-service-continuous-deployment/select-build-provider.png)
   
1. 在 **「設定**」頁上,在 **「代碼」** 部分中,選擇要連續部署**的組織**、**儲存庫**和**分支**,然後選擇「**繼續**」。
     
     > [!NOTE]
     > 如果看不到任何存儲庫,則可能需要在 GitHub 中授權 Azure 應用服務。 瀏覽到您的 GitHub 儲存函式庫,然後轉到**設定** > **應用程式** > **授權 OAuth 應用程式**。 選擇**Azure 應用服務**,然後選擇 **"授予**"。 對於組織存儲庫,您必須是組織的擁有者才能授予許可權。
       
    在 **"產生'** 部分中,指定 Azure 管道應用於執行產生任務的語言框架 Azure DevOps 組織、專案語言框架,然後選擇「**繼續**」。

   ![產生提供者](media/app-service-continuous-deployment/build-configure.png)

1. 配置生成提供程式後,請查看 **「摘要」** 頁上的設置,然後選擇 **「完成**」 。

   ![產生提供者](media/app-service-continuous-deployment/summary.png)
   
1. 所選存儲庫和分支中的新提交現在連續部署到應用服務中。 您可以在 **「部署中心**」頁上追蹤提交和部署。
   
   ![追蹤部署中心的提交並部署](media/app-service-continuous-deployment/github-finished.png)

#### <a name="azure-repos--azure-pipelines"></a>Azure 儲存函式庫 + Azure 管道

1. 在[Azure 門戶](https://portal.azure.com)中,搜索**應用服務**,然後選擇要部署的應用服務。 
   
1. 在應用頁面上,選擇左側功能表中的 **「部署中心**」。

1. 選擇**Azure 儲存函式庫**作為**部署中心**頁上的原始程式碼管理提供程式,然後選擇「**繼續**」。

    ![原始檔控制](media/app-service-continuous-deployment/deployment-center-src-control.png)

1. 產生**提供程式**頁上,選擇**Azure 管道(預覽),** 然後選擇「**繼續**」。

    ![原始檔控制](media/app-service-continuous-deployment/azure-pipelines.png)

1. 在 **「設定**」頁上,在 **「代碼」** 部分中,選擇要連續部署**的組織**、**儲存庫**和**分支**,然後選擇「**繼續**」。

   > [!NOTE]
   > 如果未列出現有的 Azure DevOps 組織,則可能需要將其連結到 Azure 訂閱。 有關詳細資訊,請參閱訂[CD 發行管道](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)。

   在 **"產生'** 部分中,指定 Azure 管道應用於執行產生任務的語言框架 Azure DevOps 組織、專案語言框架,然後選擇「**繼續**」。

   ![產生提供者](media/app-service-continuous-deployment/build-configure.png)

1. 配置生成提供程式後,請查看 **「摘要」** 頁上的設置,然後選擇 **「完成**」 。  
     
   ![產生提供者](media/app-service-continuous-deployment/summary-azure-pipelines.png)

1. 所選存儲庫和分支中的新提交現在連續部署到應用服務中。 您可以在 **「部署中心**」頁上追蹤提交和部署。

## <a name="disable-continuous-deployment"></a>停用連續部署

要禁用連續部署,請在應用**的部署中心**頁面頂部選擇 **「斷開連接**」。

![停用連續部署](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>使用不支援的儲存函式庫

對於 Windows 應用,您可以手動設定來自門戶沒有直接支援的雲端 Git 或 Mercurial 儲存庫的連續部署,例如[GitLab](https://gitlab.com/)。 通過選擇 **「部署中心**」頁中的「外部」框來執行此操作。 有關詳細資訊,請參閱[使用手動步驟設定連續部署](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)。

## <a name="additional-resources"></a>其他資源

* [調查持續部署的常見問題](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [使用 Azure 電源外殼](/powershell/azureps-cmdlets-docs)
* [Git 文件](https://git-scm.com/documentation)
* [專案 Kudu](https://github.com/projectkudu/kudu/wiki)

[建立儲存機制 (GitHub)]: https://help.github.com/articles/create-a-repo
[建立儲存機制 (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[建立新的 Git 儲存函式庫(Azure 儲存函式庫)]: /azure/devops/repos/git/creatingrepo
