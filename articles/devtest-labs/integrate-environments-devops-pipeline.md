---
title: 將環境整合至 Azure DevTest Labs 中的 Azure Pipelines
description: 瞭解如何將 Azure DevTest Labs 環境整合至 Azure DevOps 持續整合 (CI) 和持續傳遞 (CD) 管線。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: be726b2a3f67fd3dada4fdc3cf794922a3c18d06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85483018"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>將環境整合到您的 Azure DevOps CI/CD 管線
您可以使用 Azure DevOps Services (中所安裝的 Azure DevTest Labs Tasks 擴充功能（先前稱為 Visual Studio Team Services) ）輕鬆地將持續整合 (CI) /持續傳遞 (CD) 組建和發行管線與 Azure DevTest Labs 整合。 這些延伸模組可讓您更輕鬆地快速部署特定測試工作的 [環境](devtest-lab-test-env.md) ，然後在測試完成時將其刪除。 

本文說明如何建立和部署環境，然後刪除環境，全都放在一個完整的管線中。 您通常會在自己的自訂群組建-測試-部署管線中個別執行這些工作。 本文中使用的擴充功能除了這些 [建立/刪除 DTL VM](devtest-lab-integrate-ci-cd.md)工作之外：

- 建立環境
- 刪除環境

## <a name="before-you-begin"></a>開始之前
將您的 CI/CD 管線與 Azure DevTest Labs 整合之前，請先從 Visual Studio Marketplace 安裝 [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) 擴充功能。 

## <a name="create-and-configure-the-lab-for-environments"></a>為環境建立及設定實驗室
本節說明如何建立及設定將部署 Azure 環境的實驗室。

1. 如果您還沒有實驗室，請[建立](devtest-lab-create-lab.md)一個。 
2. 依照下列文章中的指示設定實驗室並建立環境範本： [使用 Azure Resource Manager 範本建立多個 VM 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)。
3. 在此範例中，請使用現有的 Azure 快速入門範本 [https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/) 。
4. 將 **201-redis-** ----------------資料庫檔案夾複製到步驟2中設定之存放庫的 **ArmTemplate** 資料夾中。

## <a name="create-a-release-definition"></a>建立發行定義
若要建立發行定義，請執行下列動作：

1.  在**組建 & 發行中樞**的 [**發行**] 索引標籤上，選取**加號 (+) **按鈕。
2.  在 [建立發行定義]**** 視窗中，選取 [空白]**** 範本，然後選取 [下一步]****。
3.  選取 [稍後選擇]****，然後選取 [建立]****，以使用一個預設的環境、但不使用任何連結的構件來建立新的發行定義。
4.  若要開啟快捷方式功能表，請在新的發行定義中，選取環境名稱旁邊的 **省略號 ( ... ) ** ，然後選取 [ **設定變數**]。
5.  在 [設定 - 環境]**** 視窗中，為您在發行定義工作中使用的變數輸入下列值：
1.  針對 [ **administratorLogin**]，輸入 SQL 系統管理員登入名稱。
2.  針對 [ **administratorLoginPassword**]，輸入 SQL 系統管理員登入所要使用的密碼。 使用「掛鎖」圖示可隱藏及保護密碼。
3.  針對 [ **資料庫**名稱]，輸入 SQL Database 名稱。
4.  這些變數是範例環境的特定變數，不同的環境可能會有不同的變數。

## <a name="create-an-environment"></a>建立環境
部署的下一個階段是建立要用於開發或測試用途的環境。

1. 在發行定義中，選取 [新增工作]****。
2. **在 [工作**] 索引標籤上，新增 Azure DevTest Labs 建立環境工作]。 請依照下列方式設定工作：
    1. 針對 [Azure RM 訂用帳戶]****，選取 [可用的 Azure 服務連線]**** 清單中的連線，或對您的 Azure 訂用帳戶建立權限更具限制性的連線。 如需詳細資訊，請參閱 [Azure Resource Manager 服務端點](/azure/devops/pipelines/library/service-endpoints)。
2. 針對 [ **實驗室名稱**]，選取您稍早建立的實例名稱 *。
3. 在 [存放 **庫名稱**] 中，選取已將 Resource Manager 範本 (201) 推送至 * 的儲存機制。
4. 在 [ **範本名稱**] 中，選取您儲存至原始程式碼存放庫的環境名稱 *。 
5. **實驗室名稱**、存放**庫名稱**和**範本名稱**是 Azure 資源識別碼的易記表示。 手動輸入易記名稱將會導致失敗，請使用下拉式清單來選取資訊。
6. 在 [ **環境名稱**] 中輸入名稱，以唯一識別實驗室內的環境實例。  它在實驗室中必須是唯一的。
7. **參數**檔和**參數**，可允許將自訂參數傳遞至環境。 其中一個或兩個都可以用來設定參數值。 在此範例中，將會使用 Parameters 區段。 使用您在環境中定義的變數名稱，例如： `-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. 您可以在範本的輸出區段中傳遞環境範本內的資訊。 檢查 **根據環境範本輸出建立輸出變數** ，讓其他工作可以使用資料。 `$(Reference name.Output Name)` 是要遵循的模式。 例如，如果參考名稱是 DTL，而範本中的輸出名稱是位置，則變數會是 `$(DTL.location)` 。

## <a name="delete-the-environment"></a>刪除環境
最後一個階段是刪除您在 Azure DevTest Labs 實例中部署的環境。 您通常會在執行開發工作，或在部署的資源上執行所需的測試之後，刪除環境。

在發行定義中，選取 [ **新增**工作]，然後在 [ **部署** ] 索引標籤上，新增 **Azure DevTest Labs 刪除環境** 工作。 進行下列設定：

1. 若要刪除 VM，請參閱 [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)：
    1. 針對 [Azure RM 訂用帳戶]****，選取 [可用的 Azure 服務連線]**** 清單中的連線，或對您的 Azure 訂用帳戶建立權限更具限制性的連線。 如需詳細資訊，請參閱 [Azure Resource Manager 服務端點](/azure/devops/pipelines/library/service-endpoints)。
    2. 針對 [ **實驗室名稱**]，選取環境所在的實驗室。
    3. 在 [ **環境名稱**] 中，輸入要移除之環境的名稱。
2. 輸入發行定義的名稱，並加以儲存。

## <a name="next-steps"></a>後續步驟
查看下列文章： 
- [使用 Resource Manager 範本建立多個 VM 環境](devtest-lab-create-environment-from-arm.md)。
- 從 [DevTest Labs GitHub 存放庫](https://github.com/Azure/azure-quickstart-templates)DevTest Labs 自動化的快速入門 Resource Manager 範本。
- [VSTS 疑難排解頁面](/azure/devops/pipelines/troubleshooting)

