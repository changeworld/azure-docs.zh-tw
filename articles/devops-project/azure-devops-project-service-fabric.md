---
title: 教學課程：使用 Azure DevOps 入門版將 ASP.NET Core 應用程式部署至 Azure Service Fabric
description: Azure DevOps 入門版可供輕鬆地開始使用 Azure。 透過 Azure DevOps Projects，您可以藉由幾個快速步驟將 ASP.NET Core 應用程式部署至 Azure Service Fabric。
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 723f46652643883dc8e718468a9ca9eead50e1aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856019"
---
# <a name="tutorial-deploy-your-aspnet-core-app-to-azure-service-fabric-by-using-azure-devops-starter"></a>教學課程：使用 Azure DevOps 入門版將 ASP.NET Core 應用程式部署至 Azure Service Fabric

Azure DevOps 入門版提供簡化的體驗，其可供自備現有程式碼和 Git 存放庫或選擇範例應用程式，以在 Azure 中建立持續整合 (CI) 和持續傳遞 (CD) 管線。 

DevOps 入門版也會：

* 自動建立 Azure 資源，例如 Azure Service Fabric。
* 在設定 CI/CD 管線的 Azure DevOps 中建立並設定發行管線。
* 建立用於監視的 Azure Application Insights 資源。

在本教學課程中，您將：

> [!div class="checklist"]
> * 使用 DevOps 入門版建立 ASP.NET Core 應用程式，並將應用程式部署至 Service Fabric
> * 設定 Azure DevOps 與 Azure 訂用帳戶 
> * 檢查 CI 管線
> * 檢查 CD 管線
> * 將變更認可至 Git 並自動部署至 Azure
> * 清除資源

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 您可以透過 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) 取得免費的訂用帳戶。

## <a name="use-devops-starter-to-create-an-aspnet-core-app-and-deploy-it-to-service-fabric"></a>使用 DevOps 入門版建立 ASP.NET Core 應用程式，並將應用程式部署至 Service Fabric

DevOps 入門版會在 Azure Pipelines 中建立 CI/CD 管線。 您可以建立新的 Azure DevOps 組織或使用現有組織。 DevOps 入門版也會在所選的 Azure 訂用帳戶中建立 Azure 資源，例如 Service Fabric 叢集。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在搜尋方塊中，鍵入並選取 **DevOps 入門版**。 按一下 [新增] 以建立新項目。

    ![DevOps 入門版儀表板](_img/azure-devops-starter-aks/search-devops-starter.png) 

1. 選取 [.NET]，然後選取 [下一步]。

1. 在 [選擇應用程式架構] 下 選取 [ASP.NET Core]，然後選取 [下一步]。

1. 選取 [Service Fabric 叢集]，然後選取 [下一步]。 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>設定 Azure DevOps 與 Azure 訂用帳戶

1. 建立新的 Azure DevOps 組織或選取現有組織。 

1. 為您的 Azure DevOps 專案輸入名稱。 

1. 選取 Azure 訂用帳戶。

1. 若要檢視其他 Azure 組態設定，並識別 [Service Fabric 叢集] 的 [節點虛擬機器大小] 和 [作業系統]，請選取 [變更]。 此窗格會顯示各種選項，可用來設定 Azure 服務的類型和位置。
 
1. 退出 Azure 組態區域，然後選取 [完成]。  
    程序完成需要幾分鐘的時間。 系統會在 Azure DevOps 組織的 Git 存放庫中設定 ASP.NET Core 應用程式範例、建立 Service Fabric 叢集、執行 CI/CD 管線，然後將應用程式部署至 Azure。 

    當這些動作都完成後，Azure 入口網站中即會顯示 DevOps 入門版儀表板。 您可直接從 Azure 入口網站中的 [所有資源] 移至 DevOps 入門版儀表板。 

    此儀表板會顯示您的 Azure DevOps 程式碼存放庫、CI/CD 管線和 Service Fabric 叢集。 您可以在 Azure Repos 中設定其他 CI/CD 管線選項。 從右側選取 [瀏覽] 以檢視執行中應用程式。

## <a name="examine-the-ci-pipeline"></a>檢查 CI 管線

DevOps 入門版會在 Azure Pipelines 中自動設定 CI/CD 管線。 您可以瀏覽及自訂管線。 若要讓您自己熟悉管線，請執行下列作業：

1. 移至 DevOps 入門版儀表板。

1. 在 DevOps 入門版儀表板頂端選取 [建置管線]。 瀏覽器索引標籤會顯示新專案的建置管線。

1. 指向 [狀態] 欄位，然後選取省略符號 (...)。功能表會顯示數個選項，例如將新建置排入佇列、暫停建置和編輯建置管線。

1. 選取 [編輯]。

1. 在此窗格中，您可以檢查建置管線的各種工作。 建置會執行各種工作，例如從 Git 存放庫擷取來源、還原相依性，以及發佈用來進行部署的輸出。

1. 在建置管線的頂端，選取建置管線名稱。 

1. 在建置管線名稱下，選取 [記錄]。 此窗格會顯示近期建置變更的稽核線索。 Azure DevOps 會追蹤對建置管線進行的任何變更，且可讓您比較版本。

1. 選取 [觸發程序]。 DevOps 入門版已自動建立 CI 觸發程序，且每次對存放庫的認可都會啟動新組建。 您可以選擇性地選擇要在 CI 程序中包含還是排除分支。

1. 選取 [保留期]。 根據案例，您可以指定原則來保留或移除特定數目的組建。

## <a name="examine-the-cd-pipeline"></a>檢查 CD 管線

DevOps 入門版會自動建立並設定必要的步驟，以從 Azure DevOps 組織部署至 Azure 訂用帳戶。 這些步驟包括設定 Azure 服務連線，以對 Azure 訂用帳戶驗證 Azure DevOps。 自動化也會建立發行管線，以將 CD 提供給 Azure。 若要深入了解發行管線，請執行下列動作：

1. 選取 [建置及發行]，然後選取 [版本]。 DevOps 入門版會建立發行管線來管理對 Azure 的部署。

1. 選取發行管線旁邊的省略符號 (...)，然後選取 [編輯]。 發行管線中包含 [管線]，它會定義發行程序。

1. 在 [成品] 下，選取 [置放]。 您先前檢查的建置管線會產生用於成品的輸出。 

1. 在 [置放] 圖示的右側，選取 [持續部署觸發程序]。 這個發行管線已啟用 CD 觸發程序，每次有新的建置成品可用時，它就會執行部署。 您可以選擇性地停用觸發程序，因此需要手動執行部署。 

1. 從右側選取 [檢視版本]，以顯示版本的歷程記錄。

1. 選取發行旁邊的省略符號 (...)，然後選取 [開啟]。 您可以瀏覽數個功能表，例如版本摘要、相關聯的工作項目及測試。

1. 選取 [認可]。 此檢視會顯示與此部署相關聯的程式碼認可。 請比較版本，以查看部署的認可差異。

1. 選取 [記錄]。 記錄包含關於部署程序的實用資訊。 您可以在部署期間和部署之後加以檢視。

## <a name="commit-changes-to-git-and-automatically-deploy-them-to-azure"></a>將變更認可至 Git 並自動部署至 Azure 

 > [!NOTE]
 > 下列程序會進行簡單的文字變更來測試 CI/CD 管線。

您現在已準備好要使用 CI/CD 程序來與小組共同對應用程式執行作業，並將您最新的工作自動部署至網站。 對 Git 存放庫進行的每項變更都會啟動一個建置，且發行會將變更部署至 Azure。 請遵循本節中的程序，或使用另一個方法來認可存放庫的變更。 例如，您可以將 Git 存放庫複製到您最常用的工具或 IDE 中，然後推送此存放庫的變更。

1. 在 [Azure DevOps] 功能表中，選取 [程式碼] > [檔案]，然後移至您的存放庫。

1. 移至 Views\Home 目錄，選取 Index.cshtml 檔案旁的省略符號 (...)，然後選取 [編輯]。

1. 對檔案進行變更，例如，在其中一個 div 標記內新增某些文字。 

1. 在右上方選取 [認可]，然後再次選取 [認可]來推送您的變更。  
    不久之後，會有建置啟動，然後會執行發行以部署變更。 您可在 DevOps 入門版儀表板上或在瀏覽器中，使用 Azure DevOps 即時記錄來監視組建狀態。

1. 發行完成之後，請重新整理應用程式，以確認您的變更。

## <a name="clean-up-resources"></a>清除資源

如果要進行測試，您可以清除資源，以避免產生費用。 如果不再需要，您可以刪除在此教學課程中建立的 Azure Service Fabric 叢集和相關資源。 若要這樣做，請使用 DevOps 入門版儀表板的 [刪除] 功能。

> [!IMPORTANT]
> 下列程序會永久刪除資源。 [刪除] 功能會終結 Azure DevOps 入門版專案在 Azure 和 Azure DevOps 中建立的資料，且將無法擷取這些資料。 請務必在仔細閱讀提示之後，再使用此程序。

1. 在 Azure 入口網站中，移至 DevOps 入門版儀表板。
1. 在右上方，選取 [刪除]。 
1. 在提示中，請選取 [是] 以*永久刪除*資源。

## <a name="next-steps"></a>後續步驟

您可以選擇性地修改 Azure CI/CD 管線，以符合小組的需求。 您也可以使用此 CI/CD 模式作為其他管線的範本。 在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 使用 DevOps 入門版建立 ASP.NET Core 應用程式，並將應用程式部署至 Service Fabric
> * 設定 Azure DevOps 與 Azure 訂用帳戶 
> * 檢查 CI 管線
> * 檢查 CD 管線
> * 將變更認可至 Git 並自動部署至 Azure
> * 清除資源

若要深入了解 Service Fabric 和微服務，請參閱：

> [!div class="nextstepaction"]
> [使用微服務方法建置應用程式](/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)