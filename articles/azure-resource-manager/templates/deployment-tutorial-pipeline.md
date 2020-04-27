---
title: 透過 Azure Pipelines 的持續整合
description: 了解如何持續建置、測試及部署 Azure Resource Manager 範本。
ms.date: 04/22/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: d1c56ce913a1b63bab90f5dd5aaada382abbf493
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084323"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>教學課程：Azure Resource Manager 範本與 Azure Pipelines 的持續整合

在[上一個教學課程](./deployment-tutorial-linked-template.md)中，您部署了連結的範本。  在本教學課程中，您將了解如何使用 Azure Pipelines 持續建置及部署 Azure Resource Manager 範本專案。

Azure DevOps 提供開發人員服務，以支援小組規劃工作、共同作業開發程式碼，以及建置和部署應用程式。 開發人員可以使用 Azure DevOps Services 在雲端工作。 Azure DevOps 提供一組整合的功能，您可以透過網頁瀏覽器或 IDE 用戶端存取使用。 Azure Pipeline 就是其中一個功能。 Azure Pipelines 是一個功能齊全的持續整合 (CI) 和持續傳遞 (CD) 服務。 它可以與您慣用的 Git 提供者搭配使用，而且能夠部署到大多數的主流雲端服務。 然後您可以將程式碼的建置、測試和部署到 Microsoft Azure、Google Cloud Platform，或 Amazon Web Services 等等所有工作自動化。

> [!NOTE]
> 選擇專案名稱。 當您進行此教學課程時，請將任何 **AzureRmPipeline** 取代為您的專案名稱。
> 此專案名稱可用來產生資源名稱。  其中一項資源是儲存體帳戶。 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能使用數字和小寫字母。 名稱必須是唯一的。 在範本中，儲存體帳戶名稱是附加了 "store" 的專案名稱，且專案名稱的長度必須介於 3 到 11 個字元之間。 因此，專案名稱必須符合儲存體帳戶名稱需求，且少於 11 個字元。

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

* **GitHub 帳戶**，您會用它為您的範本建立存放庫。 如果您沒有存放庫，您可以[免費建立一個](https://github.com)。 如需使用 GitHub 存放庫的詳細資訊，請參閱[建置 GitHub 存放庫](/azure/devops/pipelines/repos/github)。
* **安裝 Git**。 此教學課程的指示使用 *Git Bash* 或 *Git Shell*。 如需指示，請參閱 [安裝 Git]( https://www.atlassian.com/git/tutorials/install-git)。
* **Azure DevOps 組織**。 如果您沒有組織，您可以免費建立一個。 請參閱[建立組織或專案集合]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)。
* (選擇性) **Visual Studio Code 搭配 Resource Manager Tools 擴充功能**。 請參閱[使用 Visual Studio Code 建立 Azure Resource Manager 範本](use-vs-code-to-create-template.md)。

## <a name="prepare-a-github-repository"></a>準備 GitHub 存放庫

GitHub 可用來儲存專案原始程式碼，包括 Resource Manager 範本。 對於其他支援的存放庫，請參閱[Azure DevOps 支援的存放庫](/azure/devops/pipelines/repos/?view=azure-devops)。

### <a name="create-a-github-repository"></a>建立 GitHub 存放庫

如果您沒有 GitHub 帳戶，請參閱[必要條件](#prerequisites)。

1. 登入 [GitHub](https://github.com)。
1. 在右上角，選取您的帳戶圖示，然後選取 [您的存放庫]  。

    ![Azure Resource Manager Azure DevOps Azure Pipelines 建立 GitHub 存放庫](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-github-repository.png)

1. 選取 [新建]  (綠色按鈕)。
1. 在 [存放庫名稱]  中輸入存放庫名稱。  例如，**AzureRmPipeline-repo**。 請記得將任何 **AzureRmPipeline** 取代為您的專案名稱。 進行此教學課程時，您可以選取 [公開]  或 [私人]  。 然後選取 [建立存放庫]  。
1. 請將 URL 抄寫下來。 存放庫 URL 的格式如下： **https://github.com/ [YourAccountName]/[YourRepositoryName]** 。

此存放庫稱為「遠端存放庫」  。 同一專案的每位開發人員都可以複製他/她自己的「本機存放庫」  ，並將變更合併至遠端存放庫。

### <a name="clone-the-remote-repository"></a>複製遠端存放庫

1. 開啟 Git Shell 或 Git Bash。  請參閱[必要條件](#prerequisites)。
1. 確認您目前的資料夾為 **GitHub**。
1. 執行以下命令：

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateWebApp
    cd CreateWebApp
    pwd
    ```

    將 **[YourAccountName]** 取代為您的 GitHub 帳戶名稱，並將 **[YourGitHubRepositoryName]** 取代為您在上一個程序中建立的存放庫名稱。

**CreateWebApp** 資料夾是範本儲存所在的資料夾。 可執行 **Pwd** 命令以顯示資料夾路徑。 路徑是您透過以下程序儲存範本的位置。

### <a name="download-a-quickstart-template"></a>下載快速入門範本

您可以下載範本並將其儲存至 **CreateWebApp** 資料夾，而不要建立範本。

* 主要範本： https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
* 連結的範本： https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

資料夾名稱和檔案名稱都與其在管線中使用的名稱相同。  如果您變更這些名稱，就必須更新管線中使用的名稱。

### <a name="push-the-template-to-the-remote-repository"></a>將範本推送至遠端存放庫

azuredeploy.json 已新增至本機存放庫。 接下來您會將範本推送至遠端存放庫。

1. 開啟 *Git Shell* 或 *Git Bash* (如果未開啟)。
1. 將目錄變更為您本機存放庫中的 CreateWebApp 資料夾。
1. 確認 **azuredeploy.json** 檔案位於資料夾中。
1. 執行以下命令：

    ```bash
    git add .
    git commit -m "Add web app templates."
    git push origin master
    ```

    您可能會收到關於 LF 的警告。 您可以忽略警告。 **master**為主要分支。  通常您會為每個更新建立分支。 為了簡化此教學課程，您可以直接使用 master 分支。
1. 使用瀏覽器瀏覽至您的 GitHub 存放庫。  URL 為 **https://github.com/ [YourAccountName]/[YourGitHubRepository]** 。 您應該會看到 **CreateWebApp** 資料夾，以及資料夾內的三個檔案。
1. 選取 [linkedStorageAccount.json]  以開啟範本。
1. 選取 [原始]  按鈕。 URL 的開頭為 **raw.githubusercontent.com**。
1. 複製 URL。  當您稍後在本教學課程中設定管線時，將必須提供此值。

到目前為止，您已建立一個 GitHub 存放庫，並已上傳一個範本至存放庫。

## <a name="create-a-devops-project"></a>建立 DevOps 專案

您需要一個 DevOps 的組織，才能繼續進行下一個程序。  如果您沒有 DevOps 組織，請參閱[必要條件](#prerequisites)。

1. 登入 [Azure DevOps](https://dev.azure.com)。
1. 從左側選取一個 DevOps 組織。

    ![Azure Resource Manager Azure DevOps Azure Pipelines 建立 Azure DevOps 專案](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. 選取 [新增專案]  。 如果您沒有任何專案，系統會自動開啟建立專案頁面。
1. 輸入下列值：

    * **專案名稱**：輸入專案名稱。 您可以使用您在此教學課程一開始時所選擇的專案名稱。
    * **版本控制**：選取 [Git]  。 您可能需要展開 [ 進階]  才能看到 [版本控制]  。

    其他屬性則請使用預設值。
1. 選取 [建立]  。

建立用來將專案部署至 Azure 的服務連線。

1. 按一下左側功能表的底部選取 [專案設定]  。
1. 選取 [管線]  底下的 [服務連線]  。
1. 依序選取 [新增服務連線]  、[Azure Resource Manager]  和 [下一步]  。
1. 選取 [服務主體]  ，然後選取 [下一步]  。
1. 輸入下列值：

    * **範圍層級**：選取 [訂用帳戶]  。
    * **訂用帳戶**︰選取您的訂用帳戶。
    * **資源群組**：保留為空白。
    * **連線名稱**：輸入連線名稱。 例如，**AzureRmPipeline conn**。 請記下此名稱，您在建立管線時會用到。
    * **授與所有管線的存取權限**。 (已選取)
1. 選取 [儲存]  。

## <a name="create-a-pipeline"></a>建立管線

到目前為止，我們已完成下列工作。  如果您因為已熟悉 GitHub 和 DevOps 而略過前幾節，您必須先完成工作才能繼續。

* 建立 GitHub 存放庫，並將範本儲存至存放庫中的 **CreateWebApp** 資料夾。
* 建立 DevOps 專案，並建立一個 Azure Resource Manager 服務連線。

建立包含一個部署範本步驟的管線：

1. 從左側功能表中選取 [管線]  。
1. 選取 [建立新管線]  。
1. 從 [連線]  索引標籤選取 **GitHub**。 如果系統要求輸入您的 GitHub 認證，請輸入並依照指示進行。 如果您看到以下畫面，請選取 [只選取存放庫]  ，並確認清單中包含您的存放庫，然後再選取 [核准並安裝]  。

    ![Azure Resource Manager Azure DevOps Azure Pipelines 只選取存放庫](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. 從 [選取]  索引標籤選取您的存放庫。  預設名稱為 **[YourAccountName]/[YourGitHubRepositoryName]** 。
1. 從 [設定]  索引標籤選取 [入門管線]  。 它會顯示包含兩個指令碼步驟的 **azure pipelines.yml** 管線檔案。
1. 從 yml 檔案中刪除兩個指令碼步驟。
1. 將游標移至「步驟：」  後面的那一行。
1. 選取畫面右側的 [顯示小幫手]  ，以開啟 [工作]  窗格。
1. 選取 [ARM 範本部署]  。
1. 輸入下列值：

    * **deploymentScope**：選取 [資源群組]  。 若要深入了解範圍，請參閱[部署範圍](deploy-rest.md#deployment-scope)。
    * **Azure Resource Manager 連線**：選取您先前建立的服務連線名稱。
    * 訂用帳戶  ：指定目標訂用帳戶識別碼。
    * **動作**：選取 [建立或更新資源群組]  動作，會執行 2 個動作 - 1. 如果已經提供新的資源群組名稱，就會建立一個資源群組；2. 部署指定的範本。
    * **資源群組**：輸入新的資源群組名稱。 例如，**AzureRmPipeline rg**。
    * **位置**：選取資源群組的位置，例如**美國中部**。
    * **範本位置**：選取 [連結的成品]  ，這表示工作會直接從已連線的存放庫中尋找範本檔案。
    * **範本**：輸入 **CreateWebApp/azuredeploy.json**。 如果您變更了資料夾名稱和檔案名稱，就必須變更此值。
    * **範本參數**：將此欄位保留空白。 您將在 **覆寫範本參數中指定參數值。
    * **overrideParameters**：輸入 **-projectName [EnterAProjectName] -linkedTemplateUri [EnterTheLinkedTemplateURL]** 。 請取代專案名稱和連結的範本 URL。 連結的範本 URL 是您在[建立 GitHub 存放庫](#create-a-github-repository)結束時記下的內容。
    * **部署模式**：選取 [增量]  。
    * **部署名稱**：輸入 **DeployPipelineTemplate**。 選取 [進階]  ，才能看到 [部署名稱]  。

    ![Azure Resource Manager Azure DevOps Azure Pipelines 步驟](./media/deployment-tutorial-pipeline/resource-manager-template-pipeline-configure.png)

1. 選取 [新增]  。

    如需有關工作的詳細資訊，請參閱 [Azure 資源群組部署工作](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)和 [Azure Resource Manager 範本部署工作](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)

    yml 檔案應該會顯示如下：

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-yml.png)

1. 選取 [儲存並執行]  。
1. 從 [儲存並執行]  窗格中，再次選取 [儲存並執行]  。 YAML 檔案的複本已儲存到連線的存放庫。 您可以瀏覽您的存放庫，就能看到 YAML 檔案。
1. 確認管線已成功執行。

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>驗證部署

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 開啟資源群組。 名稱是您在管線 YAML 檔案中指定的。  您應該會看到已建立一個儲存體帳戶。  儲存體帳戶名稱開頭為 **store**。
1. 選取儲存體帳戶以將它開啟。
1. 選取 [屬性]  。 請注意，[複寫]  是 [本地備援儲存體 (LRS)]  。

## <a name="update-and-redeploy"></a>更新並重新部署

當您更新範本並將變更推送至遠端存放庫時，管線會自動更新資源，也就是此範例中的儲存體帳戶。

1. 在 Visual Studio Code 或任何文字編輯器中，從本機存放庫開啟 **linkedStorageAccount.json**。
1. 將 **storageAccountType** 的 **defaultValue** 更新為 **Standard_GRS**。 請參閱下列螢幕擷取畫面：

    ![Azure Resource Manager Azure DevOps Azure Pipelines 更新 yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-update-yml.png)

1. 儲存變更。
1. 從 Git Bash/Shell 執行下列命令來將變更推送至遠端存放庫。

    ```bash
    git pull origin master
    git add .
    git commit -m "Update the storage account type."
    git push origin master
    ```

    第一個命令 (pull) 會同步處理本機存放庫與遠端存放庫。 管線 YAML 檔案僅新增至遠端存放庫。 執行 pull 命令會將 YAML 檔案的複本下載到本機分支。

    第四個命令 (push) 會將修改過的 linkedStorageAccount.json 檔案上傳至遠端存放庫。 當遠端存放庫的 master 分支更新時，就會再引發一次管線。

若要確認所做的變更，您可以檢查儲存體帳戶的複寫屬性。  請參閱[驗證部署](#verify-the-deployment)。

## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]  。
2. 在 [依名稱篩選]  欄位中輸入資源群組名稱。
3. 選取資源群組名稱。
4. 從頂端功能表中選取 [刪除資源群組]  。

您也可能會想要刪除 GitHub 存放庫和 Azure DevOps 專案。

## <a name="next-steps"></a>後續步驟

恭喜，您已完成此 Resource Manager 範本部署教學課程。 如果您對於意見反應區段有任何意見和建議，請讓我們知道。 感謝您！
您已經準備好深入了解更進階的範本相關概念。 下一個教學課程將更詳細地說明如何使用範本參考文件來協助定義要部署的資源。

> [!div class="nextstepaction"]
> [利用範本參考](./template-tutorial-use-template-reference.md)
