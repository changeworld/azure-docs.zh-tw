---
title: 使用 Azure 部署管理員來部署範本
description: 了解如何使用 Resource Manager 範本與 Azure 部署管理員來部署 Azure 資源。
author: mumian
ms.date: 12/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: ad79721b88f886426d658ed6ee89c4969e1f1baa
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75470283"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-public-preview"></a>教學課程：使用 Azure 部署管理員搭配 Resource Manager 範本 (公開預覽)

了解如何使用 [Azure 部署管理員](./deployment-manager-overview.md)跨多個區域部署您的應用程式。 如果您偏好更快的方法，[Azure Deployment Manager 快速入門](https://github.com/Azure-Samples/adm-quickstart)會在您的訂用帳戶中建立必要的組態並且自訂成品，以在多個區域之間部署應用程式。 快速入門會執行與它在本教學課程中所做的相同工作。

若要使用部署管理員，您必須建立兩個範本：

* **拓撲範本**：說明構成應用程式的 Azure 資源及其部署位置。
* **首度發行範本**：說明部署應用程式時所應採取的步驟。

> [!IMPORTANT]
> 如果您的訂用帳戶標示針對 Canary 測試新的 Azure 功能，您只能使用 Azure 部署管理員部署到 Canary 區域。 

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 了解案例
> * 下載教學課程檔案
> * 準備成品
> * 建立使用者定義的受控識別
> * 建立服務拓撲範本
> * 建立首度發行範本
> * 部署範本
> * 驗證部署
> * 部署較新版本
> * 清除資源

其他資源：

* [Azure 部署管理員 REST API 參考](https://docs.microsoft.com/rest/api/deploymentmanager/)。
* [教學課程：在 Azure 部署管理員中使用健康情況檢查](./deployment-manager-tutorial-health-check.md)。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

若要完成本文，您需要：

* 開發 [Azure Resource Manager 範本](overview.md)的某些體驗。
* Azure PowerShell。 如需詳細資訊，請參閱[開始使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)。
* 部署管理員 Cmdlet。 若要安裝這些發行前版本的 Cmdlet，您需要最新版的 PowerShellGet。 若要取得最新版本，請參閱[安裝 PowerShellGet](/powershell/scripting/gallery/installing-psget)。 安裝 PowerShellGet 之後，請關閉 PowerShell 視窗。 開啟已提升權限的新 PowerShell 視窗，然後使用下列命令：

    ```powershell
    Install-Module -Name Az.DeploymentManager
    ```

## <a name="understand-the-scenario"></a>了解案例

服務拓撲範本會說明構成服務的 Azure 資源及其部署位置。 服務拓撲定義具有下列階層：

* 服務拓撲
  * 服務
    * 服務單位

下圖說明本教學課程中使用的服務拓撲：

![Azure 部署管理員教學課程案例圖表](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

美國西部和美國東部位置中配置了兩項服務。  每項服務分別有兩個服務單位 - Web 應用程式前端，以及作為後端的儲存體帳戶。 服務單位定義包含用來建立 Web 應用程式和儲存體帳戶的範本和參數檔案的連結。

## <a name="download-the-tutorial-files"></a>下載教學課程檔案

1. 下載本教學課程所使用的[範本和成品](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip)。
2. 將檔案解壓縮到您所在地點的電腦。

根資料夾下有兩個資料夾：

* **ADMTemplates**：包含部署管理員範本，其中含有：
  * CreateADMServiceTopology.json
  * CreateADMServiceTopology.Parameters.json
  * CreateADMRollout.json
  * CreateADMRollout.Parameters.json
* **ArtifactStore**：包含範本成品和二進位成品。 請參閱[準備成品](#prepare-the-artifacts)。

請注意，範本共有兩組。  一組是用來部署服務拓撲和首度發行的部署管理員範本，另一組則是從服務單位呼叫、用以建立 Web 服務和儲存體帳戶的範本。

## <a name="prepare-the-artifacts"></a>準備成品

下載的 ArtifactStore 資料夾包含兩個資料夾：

![Azure 部署管理員教學課程成品來源圖表](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

* **templates** 資料夾：包含範本成品。 **1.0.0.0** 和 **1.0.0.1** 分別代表兩個版本的二進位成品。 在每個版本中，兩項服務 (美國東部服務和美國西部服務) 並沒有個別的資料夾。 每個服務都有一組用來建立儲存體帳戶的範本和參數檔案，和一組用來建立 Web 應用程式的範本和參數檔案。 Web 應用程式範本會呼叫壓縮套件，其中包含 Web 應用程式檔案。 壓縮檔案是儲存在二進位資料夾中的二進位成品。
* **binaries** 資料夾：包含二進位成品。 **1.0.0.0** 和 **1.0.0.1** 分別代表兩個版本的二進位成品。 在每個版本中，都會有一個 zip 檔案用來建立美國西部位置的 Web 應用程式，和另一個 zip 檔案用來建立美國東部位置的 Web 應用程式。

兩個版本 (1.0.0.0 和 1.0.0.1) 會用於[修訂部署](#deploy-the-revision)。 雖然範本成品和二進位成品都有兩個版本，但只有二進位成品的兩個版本會有不同。 在實務上，二進位成品會比較範本成品更頻繁地更新。

1. 在文字編輯器中開啟 **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json**。 這是用來建立儲存體帳戶的基本範本。
2. 開啟 **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json**。

    ![Azure 部署管理員教學課程建立 Web 應用程式範本](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    範本會呼叫部署套件，其中包含 Web 應用程式的檔案。 在本教學課程中，壓縮套件僅包含 index.html 檔案。
3. 開啟 **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json**。

    ![Azure 部署管理員教學課程建立 Web 應用程式範本參數 containerRoot](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    deployPackageUri 的值是部署套件的路徑。 此參數包含 **$containerRoot** 變數。 $containerRoot 的值會藉由串連成品來源 SAS 位置、成品根目錄和 deployPackageUri 提供於[首度發行範本](#create-the-rollout-template)中。
4. 開啟 **\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html**。

    ```html
    <html>
      <head>
        <title>Azure Deployment Manager tutorial</title>
      </head>
      <body>
        <p>Hello world from west U.S.!</p>
        <p>Version 1.0.0.0</p>
      </body>
    </html>
    ```

    HTML 中顯示位置和版本資訊。 1\.0.0.1 資料夾中的二進位檔案會顯示「1.0.0.1 版」。 在部署服務之後，您可以瀏覽這些頁面。
5. 查看其他成品檔案。 這有助於您進一步了解案例。

範本成品由服務拓撲範本使用，二進位成品則由首度發行範本使用。 拓撲範本和首度發行範本都會定義成品來源 Azure 資源，此資源會用來將 Resource Manager 指向部署中使用的範本和二進位成品。 為了簡化本教學課程，我們使用一個儲存體帳戶來儲存範本成品和二進位成品。 這兩個成品來源指向相同的儲存體帳戶。

執行下列 PowerShell 指令碼來建立資源群組、建立儲存體容器、建立 Blob 容器、上傳下載的檔案，然後建立 SAS 權杖。

> [!IMPORTANT]
> PowerShell 指令碼中的 **projectName** 用來為本教學課程中部署的 Azure 服務產生名稱。 不同的 Azure 服務有不同的名稱需求。 若要確保成功部署，請選擇小於 12 個字元且僅有小寫字母和數字的名稱。
> 儲存專案名稱的複本。 您會在整個教學課程中使用相同的 projectName。

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$filePath = Read-Host -Prompt "Enter the folder that contains the downloaded files"


$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$containerName = "admfiles"
$filePathArtifacts = "${filePath}\ArtifactStore"

New-AzResourceGroup -Name $resourceGroupName -Location $location

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$storageContext = $storageAccount.Context

$storageContainer = New-AzStorageContainer -Name $containerName -Context $storageContext -Permission Off


$filesToUpload = Get-ChildItem $filePathArtifacts -Recurse -File

foreach ($x in $filesToUpload) {
    $targetPath = ($x.fullname.Substring($filePathArtifacts.Length + 1)).Replace("\", "/")

    Write-Verbose "Uploading $("\" + $x.fullname.Substring($filePathArtifacts.Length + 1)) to $($storageContainer.CloudBlobContainer.Uri.AbsoluteUri + "/" + $targetPath)"
    Set-AzStorageBlobContent -File $x.fullname -Container $storageContainer.Name -Blob $targetPath -Context $storageContext | Out-Null
}

$token = New-AzStorageContainerSASToken -name $containerName -Context $storageContext -Permission rl -ExpiryTime (Get-date).AddMonths(1)

$url = $storageAccount.PrimaryEndpoints.Blob + $containerName + $token

Write-Host $url
```

使用 SAS 權杖複製 URL 複本。 此 URL 必須填入下列兩個參數檔案的欄位中：拓樸參數檔案和首度發行參數檔案。

從 Azure 入口網站開啟容器，並確認 **binaries** 和 **templates** 資料夾和檔案已上傳。

## <a name="create-the-user-assigned-managed-identity"></a>建立使用者指派的受控識別

在教學課程的後續內容中，您會部署首度發行。 必須要有使用者指派的受控識別，才能執行部署動作 (例如，部署 Web 應用程式和儲存體帳戶)。 此身分識別必須有權存取服務要部署到的 Azure 訂用帳戶，並且有足夠的權限可完成成品部署。

您必須建立使用者指派的受控識別，並為您的訂用帳戶設定存取控制。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 建立[使用者指派的受控識別](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。
3. 在入口網站中，從左側功能表中選取 [訂用帳戶]  ，然後選取您的訂用帳戶。
4. 選取 [存取控制 (IAM)]  ，然後選取 [新增角色指派]  。
5. 輸入或選取下列值：

    ![Azure 部署管理員教學課程指派使用者的受控識別存取控制](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    * **角色**：授與足夠的權限以完成成品部署 (Web 應用程式和儲存體帳戶)。 在本教學課程中請選取 [參與者]  。 在實務上，您可以限定為最低權限。
    * **存取權指派對象**：選取 [使用者指派的受控識別]  。
    * 選取您在先前本教學課程中建立的使用者指派受控識別。
6. 選取 [儲存]  。

## <a name="create-the-service-topology-template"></a>建立服務拓撲範本

開啟 **\ADMTemplates\CreateADMServiceTopology.json**。

### <a name="the-parameters"></a>參數

範本包含下列參數：

* **projectName**：此名稱用來建立部署管理員資源的名稱。 例如，若使用 "jdoe"，服務拓撲名稱將是 **jdoe**ServiceTopology。  資源名稱會定義在此範本的變數區段中。
* **azureResourcelocation**：為了簡化本教學課程，所有資源都會共用此位置，除非另有指定。
* **artifactSourceSASLocation**：部署的服務單位範本和參數檔案儲存所在 Blob 容器的 SAS URI。  請參閱[準備成品](#prepare-the-artifacts)。
* **templateArtifactRoot**：範本和參數儲存所在 Blob 容器的位移路徑。 預設值為 **templates/1.0.0.0**。 除非您要變更[準備成品](#prepare-the-artifacts)中說明的資料夾結構，否則請勿變更此值。 本教學課程會使用相對路徑。  完整路徑由 **artifactSourceSASLocation**、**templateArtifactRoot** 和 **templateArtifactSourceRelativePath** (或 **parametersArtifactSourceRelativePath**) 串連建構而成。
* **targetSubscriptionID**：要部署並計費的部署管理員資源的訂用帳戶識別碼。 在本教學課程中請使用您的訂用帳戶識別碼。

### <a name="the-variables"></a>變數

變數區段會定義資源的名稱、兩項服務的 Azure 位置 (**服務 WUS** 和**服務 EUS**)，以及成品路徑：

![Azure 部署管理員教學課程拓撲範本變數](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

比較成品路徑與您上傳至儲存體帳戶的資料夾結構。 請注意，成品路徑是相對路徑。 完整路徑由 **artifactSourceSASLocation**、**templateArtifactRoot** 和 **templateArtifactSourceRelativePath** (或 **parametersArtifactSourceRelativePath**) 串連建構而成。

### <a name="the-resources"></a>資源

在根層級上有兩個已定義的資源：*成品來源*和*服務拓撲*。

成品來源定義為：

![Azure 部署管理員教學課程拓撲範本資源成品來源](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

下列螢幕擷取畫面僅顯示服務拓撲、服務和服務單位定義的某些部分：

![Azure 部署管理員教學課程拓撲範本資源服務拓撲](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

* **artifactSourceId** 用來建立成品來源資源與服務拓撲資源的關聯。
* **dependsOn**：所有的服務拓撲資源皆相依於成品來源資源。
* **artifacts** 指向範本成品。  本文使用相對路徑。 完整路徑由 artifactSourceSASLocation (定義於成品來源中)、artifactRoot (定義於成品來源中) 和 templateArtifactSourceRelativePath (或 parametersArtifactSourceRelativePath) 串連建構而成。

### <a name="topology-parameters-file"></a>拓撲參數檔案

您必須建立用於拓樸範本的參數檔案。

1. 在 Visual Studio Code 或任何文字編輯器中開啟 **\ADMTemplates\CreateADMServiceTopology.Parameters**。
2. 填入參數值：

    * **projectName**：輸入含有 4-5 個字元的字串。 此名稱用來建立唯一的 Azure 資源名稱。
    * **azureResourceLocation**：如果您不確定 Azure 位置，在本教學課程中請使用 **centralus**。
    * **artifactSourceSASLocation**：輸入部署的服務單位範本和參數檔案儲存所在的根目錄 (Blob 容器) 的 SAS URI。  請參閱[準備成品](#prepare-the-artifacts)。
    * **templateArtifactRoot**：除非您變更了成品的資料夾結構，否則在本教學課程中請使用 **templates/1.0.0.0**。

> [!IMPORTANT]
> 拓撲範本和首度發行範本會共用一些通用的參數。 這些參數必須具有相同的值。 這些參數是：**projectName**、**azureResourceLocation** 和 **artifactSourceSASLocation** (在本教學課程中，兩個成品來源會共用相同的儲存體帳戶)。

## <a name="create-the-rollout-template"></a>建立首度發行範本

開啟 **\ADMTemplates\CreateADMRollout.json**。

### <a name="the-parameters"></a>參數

範本包含下列參數：

![Azure 部署管理員教學課程首度發行範本參數](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

* **projectName**：此名稱用來建立部署管理員資源的名稱。 例如，若使用 "jdoe"，首度發行檔案名稱將是 **jdoe**Rollout。  名稱會定義在範本的變數區段中。
* **azureResourcelocation**：為了簡化本教學課程，所有部署管理員資源都會共用此位置，除非另有指定。
* **artifactSourceSASLocation**：部署的服務單位範本和參數檔案儲存所在的根目錄 (Blob 容器) 的 SAS URI。  請參閱[準備成品](#prepare-the-artifacts)。
* **binaryArtifactRoot**：預設值為 **binaries/1.0.0.0**。 除非您要變更[準備成品](#prepare-the-artifacts)中說明的資料夾結構，否則請勿變更此值。 本教學課程會使用相對路徑。  完整路徑由 CreateWebApplicationParameters.json 中指定的 **artifactSourceSASLocation**、**binaryArtifactRoot** 和 **deployPackageUri** 串連建構而成。  請參閱[準備成品](#prepare-the-artifacts)。
* **managedIdentityID**：執行部署動作的使用者指派受控識別。 請參閱[建立使用者指派的受控識別](#create-the-user-assigned-managed-identity)。

### <a name="the-variables"></a>變數

變數區段會定義資源的名稱。 請確定服務拓撲名稱、服務名稱和服務單位名稱均符合[拓樸範本](#create-the-service-topology-template)中定義的名稱。

![Azure 部署管理員教學課程首度發行範本變數](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>資源

在根層級上有三個已定義的資源：成品來源、步驟和首度發行。

成品來源定義與拓樸範本中定義的完全相同。  如需詳細資訊，請參閱[建立服務拓撲範本](#create-the-service-topology-template)。

下列螢幕擷取畫面顯示等候步驟定義：

![Azure 部署管理員教學課程首度發行範本資源等候步驟](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

持續時間採用 [ISO 8601 標準](https://en.wikipedia.org/wiki/ISO_8601#Durations)。 **PT1M** (必須使用大寫字母) 是等候 1 分鐘的範例。

下列螢幕擷取畫面僅顯示首度發行定義的某些部分：

![Azure 部署管理員教學課程首度發行範本資源首度發行](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

* **dependsOn**：首度發行資源相依於成品來源資源，和任何已定義的步驟。
* **artifactSourceId**：用來建立成品來源資源與首度發行資源的關聯。
* **targetServiceTopologyId**：用來建立服務拓撲資源與首度發行資源的關聯。
* **deploymentTargetId**：這是服務拓撲資源的服務單位資源識別碼。
* **preDeploymentSteps** 和 **postDeploymentSteps**：包含首度發行步驟。 在範本中會呼叫等候步驟。
* **dependsOnStepGroups**：設定步驟群組之間的相依性。

### <a name="rollout-parameters-file"></a>首度發行參數檔案

您必須建立用於首度發行範本的參數檔案。

1. 在 Visual Studio Code 或任何文字編輯器中開啟 **\ADMTemplates\CreateADMRollout.Parameters**。
2. 填入參數值：

    * **projectName**：輸入含有 4-5 個字元的字串。 此名稱用來建立唯一的 Azure 資源名稱。
    * **azureResourceLocation**：指定 Azure 位置。
    * **artifactSourceSASLocation**：輸入部署的服務單位範本和參數檔案儲存所在的根目錄 (Blob 容器) 的 SAS URI。  請參閱[準備成品](#prepare-the-artifacts)。
    * **binaryArtifactRoot**：除非您變更了成品的資料夾結構，否則在本教學課程中請使用 **binaries/1.0.0.0**。
    * **managedIdentityID**：輸入使用者指派的受控識別。 請參閱[建立使用者指派的受控識別](#create-the-user-assigned-managed-identity)。 語法為：

        ```
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> 拓撲範本和首度發行範本會共用一些通用的參數。 這些參數必須具有相同的值。 這些參數是：**projectName**、**azureResourceLocation** 和 **artifactSourceSASLocation** (在本教學課程中，兩個成品來源會共用相同的儲存體帳戶)。

## <a name="deploy-the-templates"></a>部署範本

Azure PowerShell 可用來部署範本。

1. 執行部署服務拓撲的指令碼。

    ```azurepowershell
    # Create the service topology
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
    ```

    如果您執行此指令碼的 PowerShell 工作階段與用來執行[準備成品](#prepare-the-artifacts)指令碼的工作階段不同，則您必須先重新填入變數，包括 **$resourceGroupName** 和 **$filePath**。

    > [!NOTE]
    > `New-AzResourceGroupDeployment` 是非同步呼叫。 成功訊息只表示部署已成功開始。 若要確認部署，請參閱此程序的步驟 2 和步驟 4。

2. 使用 Azure 入口網站確認服務拓撲和基礎資源均已成功建立：

    ![Azure 部署管理員教學課程已部署的服務拓撲資源](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    必須選取 [顯示隱藏的類型]  才能檢視資源。

3. <a id="deploy-the-rollout-template"></a>部署首度發行範本：

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

4. 使用下列 PowerShell 指令碼查看首度發行進度：

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "${projectName}Rollout" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    必須安裝部署管理員 PowerShell Cmdlet，才能執行此 Cmdlet。 請參閱＜必要條件＞。 -Verbose 參數可用來查看整個輸出。

    下列範例顯示執行中狀態：

    ```
    VERBOSE:

    Status: Succeeded
    ArtifactSourceId: /subscriptions/<AzureSubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    BuildVersion: 1.0.0.0

    Operation Info:
        Retry Attempt: 0
        Skip Succeeded: False
        Start Time: 03/05/2019 15:26:13
        End Time: 03/05/2019 15:31:26
        Total Duration: 00:05:12

    Service: adm0925ServiceEUS
        TargetLocation: EastUS
        TargetSubscriptionId: <AzureSubscriptionID>

        ServiceUnit: adm0925ServiceEUSStorage
            TargetResourceGroup: adm0925ServiceEUSrg

            Step: Deploy
                Status: Succeeded
                StepGroup: stepGroup3
                Operation Info:
                    DeploymentName: 2F535084871E43E7A7A4CE7B45BE06510adm0925ServiceEUSStorage
                    CorrelationId: 0b6f030d-7348-48ae-a578-bcd6bcafe78d
                    Start Time: 03/05/2019 15:26:32
                    End Time: 03/05/2019 15:27:41
                    Total Duration: 00:01:08
                Resource Operations:

                    Resource Operation 1:
                    Name: txq6iwnyq5xle
                    Type: Microsoft.Storage/storageAccounts
                    ProvisioningState: Succeeded
                    StatusCode: OK
                    OperationId: 64A6E6EFEF1F7755

    ...

    ResourceGroupName       : adm0925rg
    BuildVersion            : 1.0.0.0
    ArtifactSourceId        : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    TargetServiceTopologyId : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/serviceTopologies/adm0925ServiceTopology
    Status                  : Running
    TotalRetryAttempts      : 0
    OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
    Services                : {adm0925ServiceEUS, adm0925ServiceWUS}
    Name                    : adm0925Rollout
    Type                    : Microsoft.DeploymentManager/rollouts
    Location                : centralus
    Id                      : /subscriptions/<SubscriptionID>/resourcegroups/adm0925rg/providers/Microsoft.DeploymentManager/rollouts/adm0925Rollout
    Tags                    :
    ```

    首度發行成功部署後，您應該會看到系統又建立了兩個資源群組，每項服務各一個。

## <a name="verify-the-deployment"></a>驗證部署

1. 開啟 [Azure 入口網站](https://portal.azure.com)。
2. 在首度發行部署所建立的新資源群組下，瀏覽至新建立的 Web 應用程式。
3. 在網頁瀏覽器中開啟 Web 應用程式。 確認 index.html 檔案的位置和版本。

## <a name="deploy-the-revision"></a>部署修訂

當 Web 應用程式有新版本 (1.0.0.1) 時， 您可以使用下列程序重新部署 Web 應用程式。

1. 開啟 CreateADMRollout.Parameters.json。
2. 將 **binaryArtifactRoot** 更新為 **binaries/1.0.0.1**。
3. 依照[部署範本](#deploy-the-rollout-template)中的指示重新部署首度發行。
4. 依照[驗證部署](#verify-the-deployment)中的指示驗證部署。 網頁應該會顯示 1.0.0.1 版本。

## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]  。
2. 使用 [依名稱篩選]  欄位，縮減在本教學課程中建立的資源群組數目。 應該會有 3-4 個：

    * **&lt;projectName>rg**：包含部署管理員資源。
    * **&lt;projectName>ServiceWUSrg**：包含 ServiceWUS 所定義的資源。
    * **&lt;projectName>ServiceEUSrg**：包含 ServiceEUS 所定義的資源。
    * 使用者定義受控識別的資源群組。
3. 選取資源群組名稱。
4. 從頂端功能表中選取 [刪除資源群組]  。
5. 重複最後兩個步驟，刪除本教學課程所建立的其他資源群組。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用 Azure 部署管理員。 若要在 Azure 部署管理員中整合健康情況監視，請參閱[教學課程：在 Azure 部署管理員中使用健康情況檢查](./deployment-manager-tutorial-health-check.md)。
