---
title: 教學課程：使用 Azure DevOps 入門部署 ASP.NET 應用程式和 Azure SQL 資料庫程式碼
description: DevOps 入門版可供輕鬆地開始使用 Azure。 透過 DevOps 入門版，您可經由幾個快速步驟來部署 ASP.NET Core 應用程式和 Azure SQL Database 程式碼。
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: e40eb9cc22cdc071381cc847b49a01d4d713653d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85318619"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-starter"></a>教學課程：使用 Azure DevOps 入門版來部署 ASP.NET 應用程式和 Azure SQL 資料庫程式碼

Azure DevOps 入門版提供簡化的體驗，其可供自備現有程式碼和 Git 存放庫，或選擇一個範例應用程式，以在 Azure 中建立持續整合 (CI) 和持續傳遞 (CD) 管線。 

DevOps 入門版也能夠：
* 自動建立 Azure 資源，例如 Azure SQL 資料庫中的資料庫。
* 在 Azure Pipelines 中建立並設定發行管線，其中包含 CI 的建置管線。
* 設定 CD 的發行管線。 
* 建立用於監視的 Azure Application Insights 資源。

在本教學課程中，您將：

> [!div class="checklist"]
> * 使用 Azure DevOps 入門版來部署 ASP.NET 應用程式和 Azure SQL 資料庫程式碼
> * 設定 Azure DevOps 與 Azure 訂用帳戶 
> * 檢查 CI 管線
> * 檢查 CD 管線
> * 將變更認可至 Azure Repos 並自動部署至 Azure
> * 連線到 Azure SQL Database 
> * 清除資源

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 您可以透過 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) 取得免費的訂用帳戶。

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-azure-sql-database"></a>在 DevOps Projects 中為 ASP.NET 應用程式和 Azure SQL Database 建立專案

DevOps 入門版會在 Azure Pipelines 中建立 CI/CD 管線。 您可以建立新的 Azure DevOps 組織或使用現有組織。 DevOps 入門版也會在所選的 Azure 訂用帳戶中建立 Azure 資源，例如 Azure SQL Database。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在搜尋方塊中，鍵入並選取 **DevOps 入門版**。 按一下 [新增] 以建立新項目。

    ![DevOps 入門版儀表板](_img/azure-devops-starter-aks/search-devops-starter.png)

1. 選取 [.NET]，然後選取 [下一步]。

1. 在 [選擇應用程式架構] 之下，選取 [ASP.NET]。

1. 選取 [新增資料庫]，然後選取 [下一步]。 您在先前步驟中選擇的應用程式架構，會指出這裡可用的 Azure 服務部署目標類型。 
    
1. 選取 [下一步] 。

## <a name="configure-azure-devops-and-an-azure-subscription"></a>設定 Azure DevOps 與 Azure 訂用帳戶

1. 建立新的 Azure DevOps 組織或選取現有組織。 

1. 為您的 Azure DevOps 專案輸入名稱。 

1. 選取 Azure 訂用帳戶服務。 (選擇性) 若要檢視其他 Azure 組態設定，以及在 [資料庫伺服器登入詳細資料] 區段中識別使用者名稱，您可以選取 [變更]。 請儲存使用者名稱以供本教學課程的後續步驟使用。 如果您執行此選用步驟，請先結束 Azure 設定區域，再選取 [完成]。
 
1. 選取 [完成] 。 此程序會在幾分鐘後完成，隨即在 Azure 入口網站中開啟 DevOps 入門版儀表板。 您可以直接從 Azure 入口網站中的 [所有資源] 瀏覽至該儀表板。 從右側選取 [瀏覽] 以檢視執行中的應用程式。
    
## <a name="examine-the-ci-pipeline"></a>檢查 CI 管線

DevOps 入門版會在 Azure Repos 中自動設定完整的 CI/CD 管線。 您可以瀏覽及自訂管線。 若要讓您自己熟悉 Azure DevOps 建置管線，請執行下列作業：

1. 在 DevOps 入門版儀表板頂端選取 [建置管線]。 瀏覽器索引標籤會顯示新專案的建置管線。

1. 指向 [狀態] 欄位，然後選取省略符號 (...)。功能表會顯示數個選項，例如將新建置排入佇列、暫停建置和編輯建置管線。

1. 選取 [編輯]。

1. 在此窗格中，您可以檢查建置管線的各種工作。 建置會執行各種工作，例如從 Git 存放庫擷取來源、還原相依性，以及發佈用來進行部署的輸出。

1. 在建置管線的頂端，選取建置管線名稱。

1. 將建置管線的名稱變更成較具描述性的名稱，並選取 [儲存並排入佇列]，然後選取 [儲存]。

1. 在建置管線名稱下，選取 [記錄]。 此窗格會顯示近期建置變更的稽核線索。 Azure Pipelines 會追蹤對建置管線進行的任何變更，且可讓您比較版本。

1. 選取 [觸發程序]。 DevOps 入門版已自動建立 CI 觸發程序，且每次對存放庫的認可都會啟動新的組建。 您可以選擇性地選擇要在 CI 程序中包含還是排除分支。

1. 選取 [保留期]。 根據案例，您可以指定原則來保留或移除特定數目的組建。

## <a name="examine-the-cd-pipeline"></a>檢查 CD 管線

DevOps 入門版會自動建立並設定必要的步驟，以從 Azure DevOps 組織部署至 Azure 訂用帳戶。 這些步驟包括設定 Azure 服務連線，以對 Azure 訂用帳戶驗證 Azure DevOps。 自動化也會建立 CD 管線，該管線會將 CD 提供給 Azure 虛擬機器。 若要深入了解 Azure DevOps CD 管線，請執行下列動作：

1. 選取 [建置及發行]，然後選取 [版本]。 DevOps 入門版會建立發行管線來管理對 Azure 的部署。

1. 選取發行管線旁邊的省略符號 (...)，然後選取 [編輯]。 發行管線中包含 [管線]，它會定義發行程序。

1. 在 [成品] 下，選取 [置放]。 您在先前步驟中檢查的建置管線會產生用於成品的輸出。 

1. 在 [置放] 圖示的右側，選取 [持續部署觸發程序]。 這個發行管線已啟用 CD 觸發程序，每次有新的建置成品可用時，它就會執行部署。 您可以選擇性地停用觸發程序，因此需要手動執行部署。 

    DevOps 入門版會設定隨機的 SQL 密碼，並用於發行管線。
    
1. 從左側選取 [變數]。 

   > [!NOTE]
   > 只有在變更 SQL Server 密碼後，才要執行下列步驟。 目前有單一密碼變數。
  
1. 在 [值] 方塊旁邊，選取掛鎖圖示、輸入新密碼，然後選取 [儲存]。

1. 從左側選取 [工作]，然後選取您的環境。 工作是您的部署程序所執行的活動，會在階段中分組。 此發行管線具有單一階段，該階段包含 [Azure App Service 部署] 和 [Azure SQL Database 部署] 工作。

1. 選取 [執行 Azure SQL] 工作，並檢查 SQL 部署所使用的各種屬性。 在 [部署套件] 下方，此工作會使用 *SQL DACPAC* 檔案。

1. 從右側選取 [檢視版本]，以顯示版本的歷程記錄。

1. 選取發行旁邊的省略符號 (...)，然後選取 [開啟]。 您可以瀏覽數個功能表，例如版本摘要、相關聯的工作項目及測試。

1. 選取 [認可]。 此檢視會顯示與此部署相關聯的程式碼認可。 請比較版本，以查看部署的認可差異。

1. 選取 [記錄]。 記錄包含關於部署程序的實用資訊。 您可以在部署期間和部署之後加以檢視。

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>將變更認可至 Azure Repos 並自動部署至 Azure 

 > [!NOTE]
 > 下列程序會以簡單的文字變更來測試 CI/CD 管線。 若要測試 SQL 部署程序，您可以選擇性地對資料表進行 SQL Server 結構描述變更。

您現在已準備好要使用 CI/CD 程序來與小組共同對應用程式執行作業，並將您最新的工作自動部署至網站。 對 Git 存放庫進行的每個變更都會在 Azure DevOps 中起始一個建置，且 CD 管線會對 Azure 執行部署。 請遵循本節中的程序，或使用另一個方法來認可存放庫的變更。 程式碼變更會起始 CI/CD 程序，並自動將您的變更部署至 Azure。

1. 在左窗格中，選取 [程式碼]，然後移至您的存放庫。

1. 移至 *SampleWebApplication\Views\Home* 目錄，選取 Index.cshtml 檔案旁的省略符號 (...)，然後選取 [編輯]。 

1. 對檔案進行變更，例如，在其中一個 div 標記內新增某些文字。 

1. 在右上方選取 [認可]，然後再次選取 [認可]來推送您的變更。 不久之後，會在 Azure DevOps 中開始建置並執行發行以部署變更。 在 DevOps 入門版儀表板或在瀏覽器中使用 Azure DevOps 組織監視組建狀態。

1. 發行完成之後，請重新整理您的應用程式，以確認您的變更。

## <a name="connect-to-azure-sql-database"></a>連線到 Azure SQL Database

您必須要有適當的權限才能連線至 Azure SQL Database。

1. 在 DevOps 入門版儀表板上，選取 [SQL Database] 前往 SQL Database 的管理頁面。
   
1. 選取 [設定伺服器防火牆]，然後選取 [新增用戶端 IP]。 

1. 選取 [儲存]。 您的用戶端 IP 現在可存取 SQL Server Azure 資源。

1. 回到 [SQL Database] 窗格。 

1. 從右側選取伺服器名稱，以瀏覽至 **SQL Server** 的組態頁面。

1. 選取 [重設密碼]，輸入 SQL Server 管理員登入的密碼，然後選取 [儲存]。 請務必保留此密碼，以便稍後在本教學課程中使用。

    現在，您可以選擇性地使用 SQL Server Management Studio 或 Visual Studio 之類的用戶端工具，連線至 SQL Server 和 Azure SQL Database。 請使用 [伺服器名稱] 屬性進行連線。

    如果您一開始在 DevOps Projects 中設定專案時，並未變更資料庫使用者名稱，則您的使用者名稱會是電子郵件地址的本機部分。 例如，如果您的電子郵件地址是 *johndoe\@microsoft.com*，您的使用者名稱就是 *johndoe*。

   > [!NOTE]
   > 如果變更 SQL 登入的密碼，則必須依照[檢查 CD 管線](#examine-the-cd-pipeline)區段中描述來變更發行管線變數中的密碼。

## <a name="clean-up-resources"></a>清除資源

如果要進行測試，您可以清除資源，以避免產生費用。 如果不再需要，您可以刪除在此教學課程中建立的 Azure SQL Database 和相關資源。 若要這樣做，請使用 DevOps 入門版儀表板的 [刪除] 功能。

> [!IMPORTANT]
> 下列程序會永久刪除資源。 [刪除] 功能會終結 Azure DevOps 入門版專案在 Azure 和 Azure DevOps 內建立的資料，且將無法擷取這些資料。 請務必在仔細閱讀提示之後，再使用此程序。

1. 在 Azure 入口網站中，前往 DevOps 入門版儀表板。
2. 在右上方，選取 [刪除]。 
3. 在提示中，請選取 [是] 以*永久刪除*資源。

## <a name="next-steps"></a>後續步驟

您可以選擇性地修改這些組建和發行管線，以符合小組的需求。 您也可以使用此 CI/CD 模式作為其他管線的範本。 在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 使用 Azure DevOps 入門版來部署 ASP.NET 應用程式和 Azure SQL 資料庫程式碼
> * 設定 Azure DevOps 與 Azure 訂用帳戶 
> * 檢查 CI 管線
> * 檢查 CD 管線
> * 將變更認可至 Azure Repos 並自動部署至 Azure
> * 連線到 Azure SQL Database 
> * 清除資源

若要深入了解 CI/CD 管線，請參閱：

> [!div class="nextstepaction"]
> [定義多階段的持續部署 (CD) 管線](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>影片

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]
