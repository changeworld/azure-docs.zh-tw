---
title: 設定連續部署
description: 瞭解如何啟用從 GitHub、BitBucket、Azure Repos 或其他存放庫 Azure App Service 的 CI/CD。 選取符合您需求的組建管線。
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 08/23/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: d58eb333c930d2ffac4eb57340ea776338325181
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266035"
---
# <a name="continuous-deployment-to-azure-app-service"></a>持續部署至 Azure App Service

[Azure App Service](overview.md)可透過提取最新的更新，從 GitHub、BitBucket 和[Azure Repos](https://azure.microsoft.com/services/devops/repos/)存放庫進行持續部署。 本文說明如何使用 Azure 入口網站，透過 Kudu 組建服務或[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)持續部署您的應用程式。 

如需原始檔控制服務的詳細資訊，請參閱建立存放庫[建立儲存機制 (GitHub)]、建立存放庫[建立儲存機制 (BitBucket)]或[建立新的 Git 存放庫（Azure Repos）]。

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>授權 Azure App Service 

若要使用 Azure Repos，請確定您的 Azure DevOps 組織已連結至您的 Azure 訂用帳戶。 如需詳細資訊，請參閱[設定 Azure DevOps Services 帳戶，使其可以部署至 web 應用程式](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)。

針對 Bitbucket 或 GitHub，授權 Azure App Service 連接到您的存放庫。 您只需要對原始檔控制服務授權一次。 

1. 在  [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 **應用程式服務**。 

   ![搜尋 [應用程式服務]。](media/app-service-continuous-deployment/search-for-app-services.png)

1. 選取您想要部署的 web 應用程式。

   ![選取您的應用程式。](media/app-service-continuous-deployment/select-your-app.png)
   
1. 在 [應用程式] 頁面上，選取左側功能表中的 [**部署中心**]。
   
1. 在 [**部署中心**] 頁面上，選取 [ **GitHub** ] 或 [ **Bitbucket**]，然後選取 [**授權**]。 
   
   ![選取 [原始檔控制服務]，然後選取 [授權]。](media/app-service-continuous-deployment/github-choose-source.png)
   
1. 視需要登入服務，並遵循授權提示。 

## <a name="enable-continuous-deployment"></a>啟用持續部署 

在您授權原始檔控制服務之後，請透過內建的[Kudu App Service 組建伺服器](#option-1-use-the-app-service-build-service)，或透過[Azure Pipelines](#option-2-use-azure-pipelines)，設定您的應用程式以進行持續部署。 

### <a name="option-1-use-the-app-service-build-service"></a>選項1：使用 App Service 組建服務

您可以使用內建的 Kudu App Service 組建伺服器，從 GitHub、Bitbucket 或 Azure Repos 持續部署。 

1. 在  [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 **應用程式服務**，然後選取您想要部署的 web 應用程式。 
   
1. 在 [應用程式] 頁面上，選取左側功能表中的 [**部署中心**]。
   
1. 在 [**部署中心**] 頁面上選取您的授權原始檔控制提供者，然後選取 [**繼續**]。 針對 GitHub 或 Bitbucket，您也可以選取 [**變更帳戶**] 來變更授權的帳戶。 
   
   > [!NOTE]
   > 若要使用 Azure Repos，請確定您的 Azure DevOps Services 組織已連結至您的 Azure 訂用帳戶。 如需詳細資訊，請參閱[設定 Azure DevOps Services 帳戶，使其可以部署至 web 應用程式](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)。
   
1. 若是 GitHub 或 Azure Repos，請在 [**組建提供者**] 頁面上，選取 [ **App Service 組建服務**]，然後選取 [**繼續**]。 Bitbucket 一律會使用 App Service 組建服務。
   
   ![選取 [App Service 組建服務]，然後選取 [繼續]。](media/app-service-continuous-deployment/choose-kudu.png)
   
1. 在 [**設定**] 頁面上：
   
   - 針對 [GitHub]，下拉並選取您想要持續部署的**組織**、存放**庫**和**分支**。
     
     > [!NOTE]
     > 如果您沒有看到任何存放庫，您可能需要在 GitHub 中授權 Azure App Service。 流覽至您的 GitHub 存放庫，並移至 [**設定**] > **應用程式** > **授權的 OAuth 應用**程式。 選取 [ **Azure App Service**]，然後選取 **[授**與]。 針對組織存放庫，您必須是組織的擁有者，才能授與許可權。
     
   - 針對 [Bitbucket]，選取您想要持續部署的 Bitbucket**小組**、存放**庫**和**分支**。
     
   - 針對 Azure Repos，請選取您想要持續部署的**Azure DevOps 組織**、**專案**、存放**庫**和**分支**。
     
     > [!NOTE]
     > 如果您的 Azure DevOps 組織未列出，請確定它已連結至您的 Azure 訂用帳戶。 如需詳細資訊，請參閱[設定 Azure DevOps Services 帳戶，使其可以部署至 web 應用程式](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)。
     
1. 選取 [繼續]。
   
   ![填入存放庫資訊，然後選取 [繼續]。](media/app-service-continuous-deployment/configure-kudu.png)
   
1. 設定組建提供者之後，請檢查 [**摘要**] 頁面上的設定，然後選取 **[完成]** 。
   
   所選存放庫和分支中的新認可現在會持續部署到您的 App Service 應用程式中。 您可以在 [**部署中心**] 頁面上追蹤認可和部署。
   
   ![在部署中心追蹤認可和部署](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>選項2：使用 Azure Pipelines 

如果您的帳戶具有必要的許可權，您可以設定 Azure Pipelines 從 GitHub 或 Azure Repos 存放庫持續部署。 如需透過 Azure Pipelines 部署的詳細資訊，請參閱[將 web 應用程式部署至 Azure App 服務](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)。

若要 Azure App Service 在 Azure DevOps 組織中建立持續傳遞 Azure Pipelines： 

- 您的 Azure 帳戶必須具有寫入 Azure Active Directory 和建立服務的許可權。 
  
- 您的 Azure 帳戶必須具有 Azure 訂用帳戶中的**擁有**者角色。

- 您必須是您想要使用之 Azure DevOps 專案中的系統管理員。

若要設定 Azure Pipelines （預覽）：

1. 在  [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 **應用程式服務**，然後選取您想要部署的 web 應用程式。 
   
1. 在 [應用程式] 頁面上，選取左側功能表中的 [**部署中心**]。
   
1. 在 [**組建提供者**] 頁面上，選取 [ **Azure Pipelines （預覽）** ]，然後選取 [**繼續**]。 
   
1. 在 [**設定**] 頁面的 [程式**代碼**] 區段中：
   
   - 針對 [GitHub]，下拉並選取您想要持續部署的**組織**、存放**庫**和**分支**。
     
     > [!NOTE]
     > 如果您沒有看到任何存放庫，您可能需要在 GitHub 中授權 Azure App Service。 流覽至您的 GitHub 存放庫，並移至 [**設定**] > **應用程式** > **授權的 OAuth 應用**程式。 選取 [ **Azure App Service**]，然後選取 **[授**與]。 針對組織存放庫，您必須是組織的擁有者，才能授與許可權。
     
   - 針對 Azure Repos，請選取您想要持續部署的**Azure DevOps 組織**、**專案**、存放**庫**和**分支**，或設定新的 Azure DevOps 組織。
     
     > [!NOTE]
     > 如果您現有的 Azure DevOps 組織未列出，您可能需要將它連結到您的 Azure 訂用帳戶。 如需詳細資訊，請參閱[定義您的 CD 發行管線](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)。
     
1. 選取 [繼續]。
   
1. 針對 Azure Repos，請在 [**組建**] 區段中，指定 Azure Pipelines 應該用來執行組建工作的語言架構，然後選取 [**繼續**]。
   
1. 在 [**測試**] 頁面上，選擇是否要啟用負載測試，然後選取 [**繼續**]。
   
1. 視您的 App Service 方案[定價層](https://azure.microsoft.com/pricing/details/app-service/plans/)而定，您可能會看到 [**部署至預備**環境] 頁面。 選擇是否要[啟用部署](deploy-staging-slots.md)位置，然後選取 [**繼續**]。
   
   > [!NOTE]
   > Azure Pipelines 不允許持續傳遞至生產位置。 這種限制可防止意外部署到生產環境。 設定持續傳遞至預備位置、驗證其中的變更，然後在您準備就緒時交換位置。
   
1. 設定組建提供者之後，請檢查 [**摘要**] 頁面上的設定，然後選取 **[完成]** 。
   
   所選存放庫和分支中的新認可現在會持續部署到您的 App Service 應用程式中。 您可以在 [**部署中心**] 頁面上追蹤認可和部署。
   
   ![在部署中心追蹤認可和部署](media/app-service-continuous-deployment/github-finished.png)

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
