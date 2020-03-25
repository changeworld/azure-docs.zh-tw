---
title: 教學課程：使用 Azure DevOps Projects 將 ASP.NET 應用程式部署至 Azure 虛擬機器
description: DevOps Projects 可讓您以幾個快速步驟輕鬆地開始使用 Azure，並將 ASP.NET 應用程式部署至 Azure 虛擬機器。
ms.author: mlearned
manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: b03fb27c46d4fd925b91b8927fdd50ff4912aaec
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "71969566"
---
# <a name="tutorial-deploy-your-aspnet-app-to-azure-virtual-machines-by-using-azure-devops-projects"></a>教學課程：使用 Azure DevOps Projects 將 ASP.NET 應用程式部署至 Azure 虛擬機器

Azure DevOps Projects 提供簡化的體驗，讓您可以自備現有程式碼和 Git 存放庫，或者選擇一個範例應用程式，在 Azure 中建立持續整合 (CI) 和持續傳遞 (CD) 管線。 

DevOps Projects 也可用於：
* 自動建立 Azure 資源，例如新的 Azure 虛擬機器 (VM)。
* 在 Azure DevOps 中建立並設定發行管線，其中包含 CI 的建置管線。
* 設定 CD 的發行管線。 
* 建立用於監視的 Azure Application Insights 資源。

在本教學課程中，您將：

> [!div class="checklist"]
> * 使用 DevOps Projects 來部署 ASP.NET 應用程式
> * 設定 Azure DevOps 與 Azure 訂用帳戶 
> * 檢查 CI 管線
> * 檢查 CD 管線
> * 將變更認可至 Azure Repos 並自動部署至 Azure
> * 設定 Azure Application Insights 監視
> * 清除資源

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 您可以透過 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) 取得免費的訂用帳戶。

## <a name="use-devops-projects-to-deploy-your-aspnet-app"></a>使用 DevOps Projects 來部署 ASP.NET 應用程式

DevOps Projects 會在 Azure Pipelines 中建立 CI/CD 管線。 您可以建立新的 Azure DevOps 組織或使用現有組織。 DevOps Projects 也會在您選擇的 Azure 訂用帳戶中建立 Azure 資源，例如虛擬機器。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在左側窗格中，選取 [新增]  。

1. 在 [搜尋] 方塊中，輸入 **DevOps Projects**，然後選取 [建立]  。

    ![DevOps Projects 儀表板](_img/azure-devops-project-github/fullbrowser.png)

1. 選取 [.NET]  ，然後選取 [下一步]  。

1. 在 [選擇應用程式架構]  下 選取 [ASP.NET]  ，然後選取 [下一步]  。  
    您在先前步驟中選擇的應用程式架構，會指出這裡可用的 Azure 服務部署目標類型。 

1. 選取虛擬機器，然後選取 [下一步]  。

## <a name="configure-azure-devops-and-an-azure-subscription"></a>設定 Azure DevOps 與 Azure 訂用帳戶

1. 建立新的 Azure DevOps 組織或選取現有組織。 

1. 為您的 Azure DevOps 專案輸入名稱。 

1. 選取 Azure 訂用帳戶服務。  
    您可以選擇性地選取 [變更]  ，然後輸入更多組態詳細資料，例如 Azure 資源的位置。
 
1. 為新的 Azure 虛擬機器資源輸入虛擬機器名稱、使用者名稱和密碼，然後選取 [完成]  。  
    在幾分鐘之後，Azure 虛擬機器便會準備就緒。 系統會在 Azure DevOps 組織的存放庫中設定 ASP.NET 應用程式範例、執行組建和發行，然後將應用程式部署至新建立的 Azure VM。 

    完成後，Azure 入口網站中便會顯示 DevOps Projects 儀表板。 您可以直接從 Azure 入口網站中的 [所有資源]  瀏覽至該儀表板。 

    此儀表板會顯示 Azure DevOps 程式碼存放庫、CI/CD 管線，以及在 Azure 中執行的應用程式。   

    ![儀表板檢視](_img/azure-devops-project-vms/dashboardnopreview.png)

DevOps Projects 會自動設定 CI 組建和發行觸發程序，而將程式碼變更部署至存放庫。 您可以在 Azure DevOps 中進一步設定其他選項。 若要檢視執行中的應用程式，請選取 [瀏覽]  。
    
## <a name="examine-the-ci-pipeline"></a>檢查 CI 管線
 
DevOps Projects 會在 Azure Pipelines 中自動設定 CI/CD 管線。 您可以瀏覽及自訂管線。 若要讓您自己熟悉組建管線，請執行下列作業：

1. 在 DevOps Projects 儀表板頂端選取 [建置管線]  。  
    瀏覽器索引標籤會顯示新專案的建置管線。

1. 指向 [狀態]  欄位，然後選取省略符號 (...)。  
    功能表會顯示數個選項，例如將新建置排入佇列、暫停建置和編輯建置管線。

1. 選取 [編輯]  。

1. 在此窗格中，您可以檢查建置管線的各種工作。  
    建置會執行各種工作，例如從 Git 存放庫擷取來源、還原相依性，以及發佈用來進行部署的輸出。

1. 在建置管線的頂端，選取建置管線名稱。

1. 將建置管線的名稱變更成較具描述性的名稱，並選取 [儲存並排入佇列]  ，然後選取 [儲存]  。

1. 在建置管線名稱下，選取 [記錄]  。  
    此窗格會顯示近期建置變更的稽核線索。 Azure DevOps 會追蹤對建置管線進行的任何變更，且可讓您比較版本。

1. 選取 [觸發程序]  。  
    DevOps Projects 會自動建立 CI 觸發程序，且每次對存放庫的認可都會啟動新的建置。 您可以選擇性地選擇要在 CI 程序中包含還是排除分支。

1. 選取 [保留期]  。  
    根據案例，您可以指定原則來保留或移除特定數目的組建。

## <a name="examine-the-cd-pipeline"></a>檢查 CD 管線

DevOps Projects 會自動建立並設定必要的步驟，以從 Azure DevOps 組織部署至 Azure 訂用帳戶。 這些步驟包括設定 Azure 服務連線，以對 Azure 訂用帳戶驗證 Azure DevOps。 自動化也會建立 CD 管線，該管線會將 CD 提供給 Azure 虛擬機器。 若要深入了解 Azure DevOps CD 管線，請執行下列動作：

1. 選取 [建置及發行]  ，然後選取 [版本]  。  
    DevOps Projects 會建立發行管線來管理 Azure 的部署。

1. 選取發行管線旁邊的省略符號 (...)，然後選取 [編輯]  。  
    發行管線中包含 [管線]  ，它會定義發行程序。

1. 在 [成品]  下，選取 [置放]  。  
    您在先前步驟中檢查的建置管線會產生用於成品的輸出。 

1. 在 [置放]  圖示旁邊，選取 [持續部署觸發程序]  。  
    這個發行管線已啟用 CD 觸發程序，每次有新的建置成品可用時，它就會執行部署。 您可以選擇性地停用觸發程序，因此需要手動執行部署。 

1. 從左側選取 [工作]  ，然後選取您的環境。  
    工作是您的部署程序所執行的活動，會在階段中分組。 此發行管線會在兩個階段中發生：
    * 第一個階段包含 Azure 資源群組部署工作，此工作會執行兩項工作：
      * 設定要用於部署的 VM
      * 將新的 VM 新增至 Azure DevOps 部署群組。 Azure DevOps 中的 VM 部署群組會管理部署目標機器的邏輯群組。
    * 在第二個階段中，IIS Web 應用程式工作會在 VM 上建立 IIS 網站。 此外也會建立一個 IIS Web 應用程式部署工作，用以部署網站。

1. 從右側選取 [檢視版本]  ，以顯示版本的歷程記錄。

1. 選取發行旁邊的省略符號 (...)，然後選取 [開啟]  。  
    您可以瀏覽數個功能表，例如版本摘要、相關聯的工作項目及測試。

1. 選取 [認可]  。  
    此檢視會顯示與此部署相關聯的程式碼認可。 請比較版本，以查看部署的認可差異。

1. 選取 [記錄]  。  
    記錄包含關於部署程序的實用資訊。 您可以在部署期間和部署之後加以檢視。

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>將變更認可至 Azure Repos 並自動部署至 Azure 

您現在已準備好要使用 CI/CD 程序來與小組共同對應用程式執行作業，並將您最新的工作自動部署至網站。 對 Git 存放庫進行的每個變更都會在 Azure DevOps 中起始一個建置，且 CD 管線會對 Azure 執行部署。 請遵循本節中的程序，或使用另一個方法來認可存放庫的變更。 程式碼變更會起始 CI/CD 程序，並自動將您的變更部署至 Azure VM 上的 IIS 網站。

1. 在左窗格中，選取 [程式碼]  ，然後移至您的存放庫。

1. 移至 Views\Home  目錄，選取 Index.cshtml  檔案旁的省略符號 (...)，然後選取 [編輯]  。

1. 對檔案進行變更，例如，在其中一個 div 標記內新增某些文字。 

1. 在右上方選取 [認可]  ，然後再次選取 [認可]  來推送您的變更。  
    不久之後，會在 Azure DevOps 中開始建置並執行發行以部署變更。 在 DevOps Projects 儀表板中或在瀏覽器中使用您的 Azure DevOps 組織監視建置狀態。

1. 發行完成之後，請重新整理您的應用程式，以確認您的變更。

## <a name="configure-azure-application-insights-monitoring"></a>設定 Azure Application Insights 監視

Azure Application Insights 可讓您輕鬆監視應用程式的效能和使用情形。 DevOps Projects 會自動為您的應用程式設定 Application Insights 資源。 您可以視需要進一步設定各種警示和監視功能。

1. 在 Azure 入口網站中，移至 DevOps Projects 儀表板。 

1. 從右下角選取應用程式的 **Application Insights** 連結。  
    **Application Insights** 窗格會隨即開啟。 此檢視包含您應用程式的使用情形、效能和可用性監視資訊。

    ![Application Insights 窗格](_img/azure-devops-project-github/appinsights.png) 

1. 選取 [時間範圍]  ，然後選取 [過去一小時]  。 選取 [更新]  以篩選結果。  
    此時您可以看到過去 60 分鐘內的所有活動。 
    
1. 若要結束此時間範圍，請選取 [x]  。

1. 選取 [警示]  ，然後選取 [新增計量警示]  。 

1. 輸入警示的名稱。

1. 在 [計量]  下拉式清單中，檢查各種警示計量。  
    預設警示是**伺服器回應時間大於 1 秒**的警示。 您可以輕鬆地設定各種警示，以改善應用程式的監視功能。

1. 選取 [透過電子郵件擁有者、參與者和讀取者進行通知]  核取方塊。  
    您也可以選擇性地執行 Azure 邏輯應用程式，以在警示顯示時執行其他動作。

1. 選取 [確定]  可建立警示。  
    不久之後，警示就會在儀表板上呈現為作用中狀態。 

1. 退出 [警示]  區域，並返回 [Application Insights]  窗格。

1. 選取 [可用性]  ，然後選取 [新增測試]  。 

1. 輸入測試名稱，然後選取 [建立]  。  
    此時會建立簡單的 Ping 測試，以確認應用程式的可用性。 幾分鐘後即會產生測試結果，且 Application Insights 儀表板會顯示可用性狀態。

## <a name="clean-up-resources"></a>清除資源

如果要進行測試，您可以清除資源，以避免產生費用。 如果不再需要，您可以刪除在此教學課程中建立的 Azure 虛擬機器和相關資源。 若要這樣做，請使用 DevOps Projects 儀表板上的 [刪除]  功能。 

> [!IMPORTANT]
> 下列程序會永久刪除資源。 「刪除」  功能會終結 Azure DevOps Projects 專案在 Azure 和 Azure DevOps 中建立的資料，而且您將無法再擷取這些資料。 請務必在仔細閱讀提示之後，再使用此程序。

1. 在 Azure 入口網站中，移至 DevOps Projects 儀表板。
1. 在右上方，選取 [刪除]  。 
1. 在提示中，請選取 [是]  以*永久刪除*資源。

您可以選擇性地修改這些組建和發行管線，以符合小組的需求。 您也可以使用此 CI/CD 模式作為其他管線的範本。 

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 使用 DevOps Projects 來部署 ASP.NET 應用程式
> * 設定 Azure DevOps 與 Azure 訂用帳戶 
> * 檢查 CI 管線
> * 檢查 CD 管線
> * 將變更認可至 Azure Repos 並自動部署至 Azure
> * 設定 Azure Application Insights 監視
> * 清除資源

若要深入了解 CI/CD 管線，請參閱：

> [!div class="nextstepaction"]
> [定義多階段的持續部署 (CD) 管線](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
