---
title: 透過 Azure Pipelines 的持續整合
description: 了解如何持續建置、測試及部署 Azure Resource Manager 範本。
author: mumian
ms.date: 10/29/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b117861a528b6983876d28d5b343ea88c2bcadc0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260678"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>教學課程：Azure Resource Manager 範本與 Azure Pipelines 的持續整合

了解如何使用 Azure Pipelines 持續建置及部署 Azure Resource Manager 範本專案。

Azure DevOps 提供開發人員服務，以支援小組規劃工作、共同作業開發程式碼，以及建置和部署應用程式。 開發人員可以使用 Azure DevOps Services 在雲端工作。 Azure DevOps 提供一組整合的功能，您可以透過網頁瀏覽器或 IDE 用戶端存取使用。 Azure Pipeline 就是其中一個功能。 Azure Pipelines 是一個功能齊全的持續整合 (CI) 和持續傳遞 (CD) 服務。 它可以與您慣用的 Git 提供者搭配使用，而且能夠部署到大多數的主流雲端服務。 然後您可以將程式碼的建置、測試和部署到 Microsoft Azure、Google Cloud Platform，或 Amazon Web Services 等等所有工作自動化。

此教學課程專為開發 Azure Resource Manager 範本，而且是 Azure DevOps Services 和 Azure Pipelines 新手使用者的開發人員所設計。 如果您已經熟悉 GitHub 和 DevOps，您可以跳到[建立管線](#create-a-pipeline)。

> [!NOTE]
> 選擇專案名稱。 當您進行此教學課程時，請將任何 **AzureRmPipeline** 取代為您的專案名稱。

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 準備 GitHub 存放庫
> * 建立 Azure DevOps 專案
> * 建立 Azure 管線
> * 驗證管線部署
> * 更新範本並重新部署
> * 清除資源

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要完成本文，您需要：

* **GitHub 帳戶**，您會用它為您的範本建立存放庫。 如果您沒有帳戶，可以[免費](https://github.com)建立一個。 如需使用 GitHub 存放庫的詳細資訊，請參閱[建置 GitHub 存放庫](/azure/devops/pipelines/repos/github)。
* **安裝 Git**。 此教學課程的指示使用 *Git Bash* 或 *Git Shell*。 如需指示，請參閱 [安裝 Git]( https://www.atlassian.com/git/tutorials/install-git)。
* **Azure DevOps 組織**。 如果您沒有組織，您可以免費建立一個。 請參閱[建立組織或專案集合]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)。
* Visual Studio Code 搭配 Resource Manager Tools 擴充功能。 請參閱[使用 Visual Studio Code 建立 Azure Resource Manager 範本](use-vs-code-to-create-template.md)。

## <a name="prepare-a-github-repository"></a>準備 GitHub 存放庫

GitHub 可用來儲存專案原始程式碼，包括 Resource Manager 範本。 對於其他支援的存放庫，請參閱[Azure DevOps 支援的存放庫](/azure/devops/pipelines/repos/?view=azure-devops)。

### <a name="create-a-github-repository"></a>建立 GitHub 存放庫

如果您沒有 GitHub 帳戶，請參閱[必要條件](#prerequisites)。

1. 登入 [GitHub](https://github.com)。
2. 在右上角，選取您的帳戶圖示，然後選取 [您的存放庫]  。

    ![Azure Resource Manager Azure DevOps Azure Pipelines 建立 GitHub 存放庫](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. 選取 [新建]  (綠色按鈕)。
1. 在 [存放庫名稱]  中輸入存放庫名稱。  例如，**AzureRmPipeline-repo**。 請記得將任何 **AzureRmPipeline** 取代為您的專案名稱。 進行此教學課程時，您可以選取 [公開]  或 [私人]  。 然後選取 [建立存放庫]  。
1. 請將 URL 抄寫下來。 存放庫 URL 會具有以下格式：

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

此存放庫稱為「遠端存放庫」  。 同一專案的每位開發人員都可以複製他/她自己的「本機存放庫」  ，並將變更合併至遠端存放庫。

### <a name="clone-the-remote-repository"></a>複製遠端存放庫

1. 開啟 Git Shell 或 Git Bash。  請參閱[必要條件](#prerequisites)。
1. 確認您目前的資料夾為 **github**。
1. 執行以下命令：

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    將 **[YourAccountName]** 取代為您的 GitHub 帳戶名稱，並將 **[YourGitHubRepositoryName]** 取代為您在上一個程序中建立的存放庫名稱。

    下列螢幕擷取畫面顯示了一個範例。

    ![Azure Resource Manager Azure DevOps Azure Pipelines 建立 GitHub bash](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

**CreateAzureStorage** 資料夾是儲存範本的資料夾。 可執行 **Pwd** 命令以顯示資料夾路徑。 路徑是您透過以下程序儲存範本的位置。

### <a name="download-a-quickstart-template"></a>下載快速入門範本

您不需要建立範本，可以改為下載[快速入門範本]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json)。 此範本會建立一個 Azure 儲存體帳戶。

1. 開啟 Visual Studio Code。 請參閱[必要條件](#prerequisites)。
2. 使用以下 URL 開啟範本︰

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. 將檔案以 **azuredeploy.json** 名稱儲存至 **CreateAzureStorage** 資料夾。 這兩個資料夾名稱和檔案名稱都和它們在管線中使用的名稱相同。  如果您變更這些名稱，就必須更新管線中使用的名稱。

### <a name="push-the-template-to-the-remote-repository"></a>將範本推送至遠端存放庫

azuredeploy.json 已新增至本機存放庫。 接下來您會將範本推送至遠端存放庫。

1. 開啟 *Git Shell* 或 *Git Bash* (如果未開啟)。
1. 將目錄變更為您本機存放庫中的 CreateAzureStorage 資料夾。
1. 確認 **azuredeploy.json** 檔案位於資料夾中。
1. 執行以下命令：

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    您可能會收到關於 LF 的警告。 您可以忽略警告。 **master**為主要分支。  通常您會為每個更新建立分支。 為了簡化此教學課程，您可以直接使用 master 分支。
1. 使用瀏覽器瀏覽至您的 GitHub 存放庫。  URL 為 **https://github.com/ [YourAccountName]/[YourGitHubRepository]** 。 您應該會看到 **CreateAzureStorage**資料夾，而且資料夾內部有 **Azuredeploy.json**。

到目前為止，您已經建立一個 GitHub 存放庫，並已上傳一個範本至存放庫。

## <a name="create-a-devops-project"></a>建立 DevOps 專案

您需要一個 DevOps 的組織，才能繼續進行下一個程序。  如果您沒有 DevOps 組織，請參閱[必要條件](#prerequisites)。

1. 登入 [Azure DevOps](https://dev.azure.com)。
1. 從左側選取一個 DevOps 組織。

    ![Azure Resource Manager Azure DevOps Azure Pipelines 建立 Azure DevOps 專案](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. 選取 [建立專案]  。 如果您沒有任何專案，系統會自動開啟建立專案頁面。
1. 輸入下列值：

    * **專案名稱**：輸入專案名稱。 您可以使用您在此教學課程一開始時所選擇的專案名稱。
    * **版本控制**：選取 [Git]  。 您可能需要展開 [ 進階]  才能看到 [版本控制]  。

    其他屬性則請使用預設值。
1. 選取 [建立專案]  。

建立用來將專案部署至 Azure 的服務連線。

1. 按一下左側功能表的底部選取 [專案設定]  。
1. 選取 [管線]  底下的 [服務連線]  。
1. 依序選取 [新增服務連線]  和 **AzureResourceManager**。
1. 輸入下列值：

    * **連線名稱**：輸入連線名稱。 例如，**AzureRmPipeline conn**。 請記下此名稱，您在建立管線時會用到。
    * **範圍層級**：選取 [訂用帳戶]  。
    * **訂用帳戶**︰選取您的訂用帳戶。
    * **資源群組**：保留為空白。
    * **允許所有的管線使用此連線**。 (已選取)
1. 選取 [確定]  。

## <a name="create-a-pipeline"></a>建立管線

到目前為止，我們已完成下列工作。  如果您因為已熟悉 GitHub 和 DevOps 而略過前幾節，您必須先完成工作才能繼續。

- 建立 GitHub 存放庫，並將[此範本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json)儲存至存放庫中的 **CreateAzureStorage** 資料夾。
- 建立 DevOps 專案，並建立一個 Azure Resource Manager 服務連線。

建立包含一個部署範本步驟的管線：

1. 從左側功能表中選取 [管線]  。
1. 選取 [建立新管線]  。
1. 從 [連線]  索引標籤選取 **GitHub**。 如果系統要求輸入您的 GitHub 認證，請輸入並依照指示進行。 如果您看到以下畫面，請選取 [只選取存放庫]  ，並確認清單中包含您的存放庫，然後再選取 [核准並安裝]  。

    ![Azure Resource Manager Azure DevOps Azure Pipelines 只選取存放庫](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. 從 [選取]  索引標籤選取您的存放庫。  預設名稱為 **[YourAccountName]/[YourGitHubRepositoryName]** 。
1. 從 [設定]  索引標籤選取 [入門管線]  。 它會顯示包含兩個指令碼步驟的 **azure pipelines.yml** 管線檔案。
1. 請將 **steps** 區段取代為以下 YAML：

    ```yaml
    steps:
    - task: AzureResourceManagerTemplateDeployment@3
      inputs:
        deploymentScope: 'Resource Group'
        ConnectedServiceName: '[EnterYourServiceConnectionName]'
        subscriptionName: '[EnterTheTargetSubscriptionID]'
        action: 'Create Or Update Resource Group'
        resourceGroupName: '[EnterANewResourceGroupName]'
        location: 'Central US'
        templateLocation: 'Linked artifact'
        csmFile: 'CreateAzureStorage/azuredeploy.json'
        deploymentMode: 'Incremental'
    ```

    看起來應該像這樣：

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    進行下列變更：

    * **deploymentScope**：從選項中選取部署範圍：`Management Group`、`Subscription` 和 `Resource Group`。 本教學課程中使用**資源群組**。 若要深入了解範圍，請參閱[部署範圍](deploy-rest.md#deployment-scope)。
    * **ConnectedServiceName**：指定您稍早建立的服務連線名稱。
    * **SubscriptionName**：指定目標訂用帳戶識別碼。
    * **動作**：**Create Or Update Resource Group** 動作會執行 2 個動作 - 1. 如果已經提供新的資源群組名稱，就會建立一個資源群組；2. 部署指定的範本。
    * **resourceGroupName**：指定一個新的資源群組名稱。 例如，**AzureRmPipeline rg**。
    * **位置**：指定資源群組的位置。
    * **templateLocation**：當已指定 [連結成品]  時，工作就會直接從已連線的存放庫尋找範本檔案。
    * **csmFile** 為範本檔案的路徑。 您不需要指定範本參數檔案，因為範本中定義的所有參數都有預設值。

    如需有關工作的詳細資訊，請參閱 [Azure 資源群組部署工作](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)和 [Azure Resource Manager 範本部署工作](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)
1. 選取 [儲存並執行]  。
1. 再選取一次 [儲存並執行]  。 YAML 檔案的複本已儲存到連線的存放庫。 您可以瀏覽您的存放庫，就能看到 YAML 檔案。
1. 確認管線已成功執行。

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>驗證部署

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 開啟資源群組。 名稱是您在管線 YAML 檔案中指定的。  您應該會看到已建立一個儲存體帳戶。  儲存體帳戶名稱開頭為 **store**。
1. 選取儲存體帳戶以將它開啟。
1. 選取 [屬性]  。 請注意，[複寫]  是 [本地備援儲存體 (LRS)]  。

    ![Azure Resource Manager Azure DevOps Azure Pipelines 入口網站驗證](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>更新並重新部署

當您更新範本並將變更推送至遠端存放庫時，管線會自動更新資源，也就是此範例中的儲存體帳戶。

1. 在 Visual Studio Code 中，從您的本機存放庫開啟 **azuredeploy.json**。
1. 將 **storageAccountType** 的 **defaultValue** 更新為 **Standard_GRS**。 請參閱下列螢幕擷取畫面：

    ![Azure Resource Manager Azure DevOps Azure Pipelines 更新 yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. 儲存變更。
1. 從 Git Bash/Shell 執行下列命令來將變更推送至遠端存放庫。

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    第一個命令會同步處理本機存放庫與遠端存放庫。 別忘了，管線 YAML 檔案已經新增至遠端存放庫。

    當遠端存放庫的 master 分支更新時，就會再引發一次管線。

若要確認所做的變更，您可以檢查儲存體帳戶的複寫屬性。  請參閱[驗證部署](#verify-the-deployment)。

## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]  。
2. 在 [依名稱篩選]  欄位中輸入資源群組名稱。
3. 選取資源群組名稱。
4. 從頂端功能表中選取 [刪除資源群組]  。

您也可能會想要刪除 GitHub 存放庫和 Azure DevOps 專案。

## <a name="next-steps"></a>後續步驟

在此教學課程中，您會建立一個 Azure Data 管線來部署 Azure Resource Manager 範本。 若要了解如何跨多個區域部署 Azure 資源，以及如何使用安全的部署實務，請參閱

> [!div class="nextstepaction"]
> [使用安全部署做法](./deployment-manager-tutorial.md)
