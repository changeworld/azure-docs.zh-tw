---
title: 快速入門：使用 Azure DevOps Projects 建立適用於 PHP 的 CI/CD 管線
description: DevOps Projects 可讓您輕鬆地開始使用 Azure。 它可協助您透過幾個簡單的步驟，在您選擇的 Azure 服務上啟動應用程式。
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 7c097c2a7247c9cad6cdce8a89059ee8d90d159e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "70899584"
---
# <a name="create-a-cicd-pipeline-for-php-with-azure-devops-projects"></a>使用 Azure DevOps Projects 建立適用於 PHP 的 CI/CD 管線

Azure DevOps Project 提供簡化的體驗，可建立 Azure 資源，並在 Azure Pipelines 中為您的 PHP 應用程式設定持續整合 (CI) 與持續傳遞 (CD) 管線。  

如果您沒有 Azure 訂用帳戶，可以透過 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) 取得一個免費的訂用帳戶。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

 DevOps Projects 會在 Azure Pipelines 中建立 CI/CD 管線。 您可以免費建立新的 Azure DevOps 組織或使用現有組織。 DevOps Projects 也會在您選擇的 Azure 訂用帳戶中建立 Azure 資源。

1. 登入 [Microsoft Azure 入口網站](https://portal.azure.com)。

1. 在左側窗格中選取 [建立資源]  圖示，然後搜尋 [DevOps Projects]  。  

3. 選取 [建立]  。

    ![啟動連續傳遞設定](_img/azure-devops-project-php/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>選取應用程式範例和 Azure 服務

1. 選取 PHP 應用程式範例。  
        PHP 範例包含數種應用程式架構的選擇。預設架構範例為 Laravel。 
        
2. 保留預設設定，然後選取 [下一步]  。  

1. 適用於容器的 Web 應用程式是預設的部署目標。  
    您先前選擇的應用程式架構，會指出這裡可用的 Azure 服務部署目標類型。  保留預設的服務，然後選取 [下一步]  。
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>設定 Azure DevOps 與 Azure 訂用帳戶 

1. 建立新的 Azure DevOps 組織或選取現有組織。 

    a. 為您在 Azure DevOps 中的專案選擇名稱。 
    
    b. 選取 Azure 訂用帳戶和位置、輸入應用程式名稱，然後選取 [完成]  。   
        在幾分鐘後，Azure 入口網站中便會顯示 DevOps Projects 儀表板。 系統會在您 Azure DevOps 組織中的存放庫中設定應用程式範例、執行建置，然後您的應用程式會部署到 Azure。 此儀表板可顯示您的程式碼存放庫、CI/CD 管線，和您在 Azure 中的應用程式。  
        
2. 選取 [瀏覽]  以檢視執行中應用程式。

    ![儀表板檢視](_img/azure-devops-project-php/dashboardnopreview.png) 
    
   DevOps Projects 會自動設定 CI 建置和發行觸發程序。  您現在已準備好利用 CI/CD 程序與小組共同進行 PHP 應用程式的作業，這個程序會自動將您的最新工作部署到網站上。

## <a name="commit-code-changes-and-execute-cicd"></a>認可程式碼變更並執行 CI/CD

 DevOps Projects 會在 Azure Repos 或 GitHub 中建立 Git 存放庫。 若要檢視存放庫並變更您應用程式的程式碼，請遵循下列步驟：

1. 在 DevOps Projects 儀表板的左側，選取您主要分支的連結。   
    此連結會開啟新建立 Git 存放庫的檢視。

1. 若要檢視存放庫複製 URL，請從瀏覽器右上方選取 [複製]  。   
    您可以在最愛的 IDE 中複製 Git 存放庫。 在接下來的幾個步驟中，請使用網頁瀏覽器，直接進行和認可主要分支的程式碼變更。

1. 從左側移至 **resources/views/welcome.blade.php** 檔案。

1. 選取 [編輯]  ，然後進行部分文字的變更。  例如，變更其中一個 div 標籤的部分文字。

1. 選取 [認可]  ，然後儲存您的變更。

1. 在瀏覽器中，移至 DevOps Projects 儀表板。  
您現在應該會看到正在進行中的建置。 您剛才所做的變更會透過 CI/CD 管線自動建置及部署。

## <a name="examine-the-cicd-pipeline"></a>檢查 CI/CD 管線

 DevOps Projects 會在 Azure Pipelines 中自動設定完整的 CI/CD 管線。 瀏覽管線，並視需要進行自訂。 若要讓自己熟悉建置和發行管線，請執行下列作業：

1. 在 DevOps Projects 儀表板頂端選取 [建置管線]  。  
    此連結會開啟瀏覽器索引標籤和新專案的建置管線。

1. 指向 [狀態]  欄位，然後選取**省略符號** (...)。  
    功能表會顯示數個選項，例如將新建置排入佇列、暫停建置和編輯建置管線。

1. 選取 [編輯]  。

1. 在此窗格中，您可以檢查建置管線的各種工作。  
    建置會執行各種工作，例如從 Git 存放庫擷取來源、還原相依性，以及發佈用來進行部署的輸出。

1. 在建置管線的頂端，選取建置管線名稱。

1. 將建置管線的名稱變更成較具描述性的名稱，並選取 [儲存並排入佇列]  ，然後選取 [儲存]  。

1. 在建置管線名稱下，選取 [記錄]  。   
    [記錄]  窗格會顯示建置上近期建置變更的稽核線索。 Azure Pipelines 會追蹤對建置管線進行的任何變更，且可讓您比較版本。

1. 選取 [觸發程序]  。  
      DevOps Projects 已自動建立 CI 觸發程序，且每次對存放庫的認可都會啟動新的建置。 您可以選擇性地選擇要在 CI 程序中包含還是排除分支。

1. 選取 [保留期]  。   
    根據案例，您可以指定原則來保留或移除特定數目的組建。

1. 選取 [建置及發行]  ，然後選取 [版本]  。  
     DevOps Projects 會建立發行管線來管理 Azure 的部署。

1. 選取發行管線旁邊的省略符號 (...)，然後選取 [編輯]  。  
    發行管線中包含管線，用來定義發行程序。 

12. 在 [成品]  下，選取 [置放]  。  
    您在先前步驟中檢查的建置管線會產生用於成品的輸出。 

1. 在 [置放]  圖示旁邊，選取 [持續部署觸發程序]  。   
    這個發行管線已啟用 CD 觸發程序，每次有新的建置成品可用時，它就會執行部署。  您可以選擇性地停用觸發程序，因此需要手動執行部署。 

1. 從左側選取 [工作]  。  
        工作是您部署程序所執行的活動。  在此範例中，會建立一個工作來部署到 Azure App Service。

1. 從右側選取 [檢視版本]  ，以顯示版本的歷程記錄。

1. 選取您其中一個版本旁的省略符號 (...)，然後選取 [開啟]  。  
        您可從這個檢視中瀏覽數個功能表，例如版本摘要、相關聯的工作項目及測試。

1. 選取 [認可]  。  
        此檢視會顯示與特定部署相關聯的程式碼認可。 

1. 選取 [記錄]  。  
        記錄包含關於部署程序的實用資訊。 您可以在部署期間和部署之後加以檢視。

## <a name="clean-up-resources"></a>清除資源

當您不再需要 Azure App Service 和其他相關的資源時，可將其刪除。 請使用 DevOps Projects 儀表板上的 [刪除]  功能。

## <a name="next-steps"></a>後續步驟

當您設定好 CI/CD 程序後，建置和發行管線會自動建立。 您可以修改這些建置和發行管線，以符合小組的需求。 若要深入了解 CI/CD 管線，請參閱此教學課程：

> [!div class="nextstepaction"]
> [自訂 CD 程序](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
