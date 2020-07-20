---
title: 使用 Azure Pipelines 建置和發行管線中的 DevTest 實驗室
description: 瞭解如何使用 Azure Pipelines 組建和發行管線中的 Azure DevTest Labs。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 71af1e0dfe205fe1028f7b82b41f3ed38ebefd3c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483069"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>使用 Azure Pipelines 建置和發行管線中的 DevTest 實驗室
本文提供如何在 Azure Pipelines 組建和發行管線中使用 DevTest Labs 的相關資訊。 

## <a name="overall-flow"></a>整體流程
基本流程是使用**組建管線**來執行下列工作：

1. 建立應用程式程式碼。
1. 在 DevTest Labs 中建立基本環境。
1. 以自訂資訊更新環境。
1. 將應用程式部署至 DevTest Labs 環境
1. 測試程式碼。 

成功完成組建之後，**發行管線**將會使用組建成品來部署預備或生產環境。 

其中一個必要的內部部署，就是可在組建成品內取得重新建立已測試的生態系統所需的所有資訊，包括 Azure 資源的設定。 因為 Azure 資源會在使用時產生成本，所以公司想要控制或追蹤這些資源的使用。 在某些情況下，用來建立和設定資源的 Azure Resource Manager 範本可能是由另一個部門（如 IT）所管理。 此外，這些範本可能會儲存在不同的儲存機制中。 它會產生一個有趣的情況，其中會建立並測試組建，而且程式碼和設定都必須儲存在組建成品中，才能在生產環境中適當地重新建立系統。 

在組建/測試階段使用 DevTest Labs 時，您可以將 Azure Resource Manager 範本和支援檔案加入至組建來源，以便在發行階段期間，用來測試的確切設定會部署到生產環境。 使用適當設定**建立 Azure DevTest Labs 環境**工作，將會在組建成品中儲存 Resource Manager 範本。 在此範例中，您將使用[教學課程：在 Azure App Service 中建立 .Net Core 和 SQL Database web 應用程式](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md)中的程式碼，在 Azure 中部署和測試 web 應用程式。

![整體流程](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>設定 Azure 資源
您必須事先建立幾個專案：

- 兩個存放庫。 第一個具有教學課程中的程式碼，以及另一個具有兩個額外 Vm 的 Resource Manager 範本。 第二個會包含基底 Azure Resource Manager 範本（現有的設定）。
- 用於部署生產環境程式碼和設定的資源群組。
- 您必須使用組建管線之設定存放庫的連線[來](devtest-lab-create-environment-from-arm.md)設定實驗室。 Resource Manager 範本必須簽入設定存放庫，如同上的 metadata.jsazuredeploy.js，讓 DevTest Labs 能夠辨識及部署範本。

組建管線會建立 DevTest Labs 環境，並部署程式碼來進行測試。

## <a name="set-up-a-build-pipeline"></a>設定組建管線
在 Azure Pipelines 中，使用[教學課程：在 Azure App Service 中建立 .Net Core 和 SQL Database web 應用程式](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md)中的程式碼，建立組建管線。 使用 [ **ASP.NET Core** ] 範本，這會填入建立、測試及發佈程式碼所需的工作。

![選取 ASP.NET 範本](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

您必須新增三個額外的工作，以在 DevTest Labs 中建立環境，並部署至環境。

![具有三個工作的管線](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>建立環境工作
在 [建立環境] 工作（**Azure DevTest Labs 建立環境**] 工作）中，使用下拉式清單來選取下列值：

- Azure 訂用帳戶
- 實驗室的名稱
- 存放庫的名稱
- 範本的名稱（顯示儲存環境的資料夾）。 

我們建議您在頁面上使用下拉式清單，而不是手動輸入資訊。 如果您手動輸入資訊，請輸入完整的 Azure 資源識別碼。 此工作會顯示易記名稱，而不是資源識別碼。 

環境名稱是顯示在 DevTest Labs 內的名稱。 它應該是每個組建的唯一名稱。 例如： **TestEnv $ （BuildId）**。 

您可以指定參數檔案或參數，將資訊傳遞至 Resource Manager 範本。 

選取 [**根據環境範本輸出建立輸出變數**] 選項，然後輸入參考名稱。 針對此範例，請輸入**BaseEnv**做為參考名稱。 當您設定下一個工作時，將會使用此 BaseEnv。 

![建立 Azure DevTest Labs 環境工作](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>填入環境工作
第二個工作（**Azure DevTest Labs 填入環境**工作）是更新現有的 DevTest Labs 環境。 建立環境工作會輸出**BaseEnv environmentResourceId** ，用來設定這項工作的環境名稱。 此範例的 Resource Manager 範本有兩個參數- **adminUserName**和**adminPassword**。 

![填入 Azure DevTest Labs 環境工作](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>App Service 部署工作
第三個工作是**Azure App Service 部署**工作。 [應用程式類型] 設定為 [ **Web 應用程式**]，而 [App Service 名稱] 設定為 **$ （網站）**。

![App Service 部署工作](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>設定發行管線
您會建立具有兩個工作的發行管線： **Azure 部署：建立或更新資源群組**和**部署 Azure App Service**。 

針對第一個工作，指定資源群組的名稱和位置。 範本位置是連結的成品。 如果 Resource Manager 範本包含連結的範本，則必須實作為自訂資源群組部署。 範本位於已發行的卸載成品中。 覆寫 Resource Manager 範本的範本參數。 您可以保留其餘設定的預設值。 

針對第二個工作 [**部署 Azure App Service**]，指定 [Azure 訂用帳戶]、選取 [**應用程式類型**的**Web 應用程式**] 和 [ **App Service 名稱**的 **$ （網站）** ]。 您可以保留其餘設定的預設值。 

## <a name="test-run"></a>測試執行
既然已設定這兩個管線，請手動將組建排入佇列，並查看其運作方式。 下一個步驟是為組建設定適當的觸發程式，並將組建連接至發行管線。

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [將 Azure DevTest Labs 整合到您的 Azure Pipelines 持續整合和傳遞管線](devtest-lab-integrate-ci-cd.md)
- [將環境整合到您的 Azure Pipelines CI/CD 管線](integrate-environments-devops-pipeline.md)
