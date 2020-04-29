---
title: 設定連續部署
description: 瞭解如何啟用從 GitHub、BitBucket、Azure Repos 或其他存放庫 Azure App Service 的 CI/CD。 選取符合您需求的組建管線。
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/20/2020
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 847de2c2c8916558d542473d9b7c80fd5552dbf7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80437139"
---
# <a name="continuous-deployment-to-azure-app-service"></a>持續部署至 Azure App Service

[Azure App Service](overview.md)可透過提取最新的更新，從 GitHub、BitBucket 和[Azure Repos](https://azure.microsoft.com/services/devops/repos/)存放庫進行持續部署。 本文說明如何使用 Azure 入口網站，透過 Kudu 組建服務或[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)持續部署您的應用程式。 

如需原始檔控制服務的詳細資訊，請參閱建立存放庫[（GitHub）]、建立存放庫[（BitBucket）]或[建立新的 Git 存放庫（Azure Repos）]。

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>授權 Azure App Service 

若要使用 Azure Repos，請確定您的 Azure DevOps 組織已連結至您的 Azure 訂用帳戶。 如需詳細資訊，請參閱[設定 Azure DevOps Services 帳戶，使其可以部署至 web 應用程式](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)。

針對 Bitbucket 或 GitHub，授權 Azure App Service 連接到您的存放庫。 您只需要對原始檔控制服務授權一次。 

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，搜尋**應用程式服務**並選取 []。

   ![搜尋 [應用程式服務]。](media/app-service-continuous-deployment/search-for-app-services.png)

1. 選取您想要部署的 App Service。

   ![選取您的應用程式。](media/app-service-continuous-deployment/select-your-app.png)
   
1. 在 [應用程式] 頁面上，選取左側功能表中的 [**部署中心**]。
   
1. 在 [**部署中心**] 頁面上，選取 [ **GitHub** ] 或 [ **Bitbucket**]，然後選取 [**授權**]。 
   
   ![選取 [原始檔控制服務]，然後選取 [授權]。](media/app-service-continuous-deployment/github-choose-source.png)
   
1. 視需要登入服務，並遵循授權提示。 

## <a name="enable-continuous-deployment"></a>啟用持續部署 

在您授權原始檔控制服務之後，請透過內建的[Kudu App Service](#option-1-kudu-app-service)組建伺服器，或透過[Azure Pipelines](#option-2-azure-pipelines)，設定您的應用程式以進行持續部署。 

### <a name="option-1-kudu-app-service"></a>選項1： Kudu App Service

您可以使用內建的 Kudu App Service 組建伺服器，從 GitHub、Bitbucket 或 Azure Repos 持續部署。 

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，搜尋**應用程式服務**，然後選取您想要部署的 App Service。 
   
1. 在 [應用程式] 頁面上，選取左側功能表中的 [**部署中心**]。
   
1. 在 [**部署中心**] 頁面上選取您的授權原始檔控制提供者，然後選取 [**繼續**]。 針對 GitHub 或 Bitbucket，您也可以選取 [**變更帳戶**] 來變更授權的帳戶。 
   
   > [!NOTE]
   > 若要使用 Azure Repos，請確定您的 Azure DevOps Services 組織已連結至您的 Azure 訂用帳戶。 如需詳細資訊，請參閱[設定 Azure DevOps Services 帳戶，使其可以部署至 web 應用程式](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)。
   
1. 若是 GitHub 或 Azure Repos，請在 [**組建提供者**] 頁面上，選取 [ **App Service 組建服務**]，然後選取 [**繼續**]。 Bitbucket 一律會使用 App Service 組建服務。
   
   ![選取 [App Service 組建服務]，然後選取 [繼續]。](media/app-service-continuous-deployment/choose-kudu.png)
   
1. 在 [**設定**] 頁面上：
   
   - 針對 [GitHub]，下拉並選取您想要持續部署的**組織**、存放**庫**和**分支**。
     
     > [!NOTE]
     > 如果您沒有看到任何存放庫，您可能需要在 GitHub 中授權 Azure App Service。 流覽至您的 GitHub 存放庫，並移至 [**設定** > ] [**應用程式** > ] [**授權 OAuth 應用** 選取 [ **Azure App Service**]，然後選取 **[授**與]。 針對組織存放庫，您必須是組織的擁有者，才能授與許可權。
     
   - 針對 [Bitbucket]，選取您想要持續部署的 Bitbucket**小組**、存放**庫**和**分支**。
     
   - 針對 Azure Repos，請選取您想要持續部署的**Azure DevOps 組織**、**專案**、存放**庫**和**分支**。
     
     > [!NOTE]
     > 如果您的 Azure DevOps 組織未列出，請確定它已連結至您的 Azure 訂用帳戶。 如需詳細資訊，請參閱[設定 Azure DevOps Services 帳戶，使其可以部署至 web 應用程式](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)。
     
1. 選取 \[繼續\]。 
   
   ![填入存放庫資訊，然後選取 [繼續]。](media/app-service-continuous-deployment/configure-kudu.png)
   
1. 設定組建提供者之後，請檢查 [**摘要**] 頁面上的設定，然後選取 **[完成]**。
   
1. 所選取存放庫和分支中的新認可，現在會持續部署到 App Service 應用程式。 您可以在 [部署中心]  頁面上追蹤認可和部署。
   
   ![在部署中心追蹤認可和部署](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-azure-pipelines"></a>選項2： Azure Pipelines 

如果您的帳戶具有必要的許可權，您可以設定從 GitHub 或 Azure Repos 持續部署 Azure Pipelines。 如需透過 Azure Pipelines 部署的詳細資訊，請參閱[將 web 應用程式部署至 Azure App 服務](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)。

#### <a name="prerequisites"></a>先決條件

如需使用 Azure Pipelines 建立持續傳遞的 Azure App Service，您的 Azure DevOps 組織應具有下列許可權： 

- 您的 Azure 帳戶必須具有寫入 Azure Active Directory 和建立服務的許可權。 
  
- 您的 Azure 帳戶必須具有 Azure 訂用帳戶中的**擁有**者角色。

- 您必須是您想要使用之 Azure DevOps 專案中的系統管理員。

#### <a name="github--azure-pipelines"></a>GitHub + Azure Pipelines

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，搜尋**應用程式服務**，然後選取您想要部署的 App Service。 
   
1. 在 [應用程式] 頁面上，選取左側功能表中的 [**部署中心**]。

1. 選取 [ **GitHub** ] 作為 [**部署中心**] 頁面上的原始檔控制提供者，然後選取 [**繼續**]。 針對**GitHub**，您可以選取 [**變更帳戶**] 來變更授權的帳戶。

    ![原始檔控制](media/app-service-continuous-deployment/deployment-center-src-control.png)
   
1. 在 [**組建提供者**] 頁面上，選取 [ **Azure Pipelines （預覽）**]，然後選取 [**繼續**]。

    ![組建提供者](media/app-service-continuous-deployment/select-build-provider.png)
   
1. 在 [**設定**] 頁面的 [程式**代碼**] 區段中，選取您想要持續部署的**組織**、存放**庫**和**分支**，然後選取 [**繼續**]。
     
     > [!NOTE]
     > 如果您沒有看到任何存放庫，您可能需要在 GitHub 中授權 Azure App Service。 流覽至您的 GitHub 存放庫，並移至 [**設定** > ] [**應用程式** > ] [**授權 OAuth 應用** 選取 [ **Azure App Service**]，然後選取 **[授**與]。 針對組織存放庫，您必須是組織的擁有者，才能授與許可權。
       
    在 [**組建**] 區段中，指定 Azure Pipelines 應該用來執行組建工作的 Azure DevOps 組織、專案、語言架構，然後選取 [**繼續**]。

   ![組建提供者](media/app-service-continuous-deployment/build-configure.png)

1. 設定組建提供者之後，請檢查 [**摘要**] 頁面上的設定，然後選取 **[完成]**。

   ![組建提供者](media/app-service-continuous-deployment/summary.png)
   
1. 所選存放庫和分支中的新認可現在會持續部署至您的 App Service。 您可以在 [部署中心]  頁面上追蹤認可和部署。
   
   ![在部署中心追蹤認可和部署](media/app-service-continuous-deployment/github-finished.png)

#### <a name="azure-repos--azure-pipelines"></a>Azure Repos + Azure Pipelines

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，搜尋**應用程式服務**，然後選取您想要部署的 App Service。 
   
1. 在 [應用程式] 頁面上，選取左側功能表中的 [**部署中心**]。

1. 選取 [ **Azure Repos** ] 作為 [**部署中心**] 頁面上的原始檔控制提供者，然後選取 [**繼續**]。

    ![原始檔控制](media/app-service-continuous-deployment/deployment-center-src-control.png)

1. 在 [**組建提供者**] 頁面上，選取 [ **Azure Pipelines （預覽）**]，然後選取 [**繼續**]。

    ![原始檔控制](media/app-service-continuous-deployment/azure-pipelines.png)

1. 在 [**設定**] 頁面的 [程式**代碼**] 區段中，選取您想要持續部署的**組織**、存放**庫**和**分支**，然後選取 [**繼續**]。

   > [!NOTE]
   > 如果您現有的 Azure DevOps 組織未列出，您可能需要將它連結到您的 Azure 訂用帳戶。 如需詳細資訊，請參閱[定義您的 CD 發行管線](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)。

   在 [**組建**] 區段中，指定 Azure Pipelines 應該用來執行組建工作的 Azure DevOps 組織、專案、語言架構，然後選取 [**繼續**]。

   ![組建提供者](media/app-service-continuous-deployment/build-configure.png)

1. 設定組建提供者之後，請檢查 [**摘要**] 頁面上的設定，然後選取 **[完成]**。  
     
   ![組建提供者](media/app-service-continuous-deployment/summary-azure-pipelines.png)

1. 所選存放庫和分支中的新認可現在會持續部署至您的 App Service。 您可以在 [部署中心]  頁面上追蹤認可和部署。

## <a name="disable-continuous-deployment"></a>停用連續部署

若要停用持續部署，請在應用程式的 [**部署中心**] 頁面頂端選取 **[中斷連線]** 。

![停用連續部署](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>使用不支援的存放庫

針對 Windows 應用程式，您可以從入口網站不直接支援的雲端 Git 或 Mercurial 存放庫手動設定持續部署，例如[GitLab](https://gitlab.com/)。 您可以選擇 [**部署中心**] 頁面中的 [外部] 方塊來執行此動作。 如需詳細資訊，請參閱[使用手動步驟設定連續部署](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)。

## <a name="additional-resources"></a>其他資源

* [調查連續部署的常見問題](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [使用 Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Git 文件](https://git-scm.com/documentation)
* [專案 Kudu](https://github.com/projectkudu/kudu/wiki)

[建立儲存機制 (GitHub)]: https://help.github.com/articles/create-a-repo
[建立儲存機制 (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[建立新的 Git 存放庫（Azure Repos）]: /azure/devops/repos/git/creatingrepo
