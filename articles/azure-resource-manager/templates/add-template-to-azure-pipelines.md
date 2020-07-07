---
title: 具有 Azure Pipelines 和範本的 CI/CD
description: 說明如何使用 Visual Studio 中的 Azure 資源群組部署專案來部署 Resource Manager 範本，以在 Azure Pipelines 中設定持續整合。
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d8eff1c7efae319106eb8a85af7823a820a0da39
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82084646"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>整合 ARM 範本與 Azure Pipelines

Visual Studio 提供 Azure 資源群組專案來建立 Azure Resource Manager （ARM）範本，並將其部署至您的 Azure 訂用帳戶。 您可以將此專案與 Azure Pipelines 整合，以進行持續整合和持續部署（CI/CD）。

有兩種方式可以使用 Azure Pipelines 部署範本：

* **新增執行 Azure PowerShell 腳本的**工作。 此選項的優點是在整個開發生命週期中提供一致性，因為您使用的是 Visual Studio 專案（Deploy-AzureResourceGroup.ps1）中所包含的相同腳本。 腳本會將專案中的成品分階段至 Resource Manager 可以存取的儲存體帳戶。 成品是專案中的專案，例如連結的範本、腳本和應用程式二進位檔。 然後，腳本會部署範本。

* **新增工作以複製和部署**工作。 此選項提供專案腳本的便利替代方案。 您可以在管線中設定兩個工作。 一個工作階段：成品和另一個工作會部署範本。

本文章說明這兩種方法。

## <a name="prepare-your-project"></a>準備您的專案

本文假設您的 Visual Studio 專案和 Azure DevOps 組織已準備好建立管線。 下列步驟顯示如何確定您已經準備就緒：

* 您有 Azure DevOps 的組織。 如果您沒有帳戶，請[免費建立一個](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)。 如果您的小組已經有 Azure DevOps 組織，請確定您是您想要使用之 Azure DevOps 專案的系統管理員。

* 您已設定 Azure 訂用帳戶的[服務](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops)連線。 管線中的工作會在服務主體的身分識別下執行。 如需建立連線的步驟，請參閱[建立 DevOps 專案](deployment-tutorial-pipeline.md#create-a-devops-project)。

* 您有一個從**Azure 資源群組**入門範本建立的 Visual Studio 專案。 如需建立該類型專案的詳細資訊，請參閱[透過 Visual Studio 建立和部署 Azure 資源群組](create-visual-studio-deployment-project.md)。

* 您的 Visual Studio 專案會[連接到 Azure DevOps 專案](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops)。

## <a name="create-pipeline"></a>建立管線

1. 如果您先前尚未新增管線，您必須建立新的管線。 從您的 Azure DevOps 組織，選取 [**管線**] 和 [**新增管線**]。

   ![加入新的管線](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. 指定您的程式碼儲存位置。 下圖顯示選取**Azure Repos Git**。

   ![選取程式碼來源](./media/add-template-to-azure-pipelines/select-source.png)

1. 從該來源選取具有您專案之程式碼的存放庫。

   ![選取存放庫](./media/add-template-to-azure-pipelines/select-repo.png)

1. 選取要建立的管線類型。 您可以選取 [**起始管線**]。

   ![選取管線](./media/add-template-to-azure-pipelines/select-pipeline.png)

您已準備好加入 Azure PowerShell 工作或複製檔案和部署工作。

## <a name="azure-powershell-task"></a>Azure PowerShell 工作

本節說明如何使用在您的專案中執行 PowerShell 腳本的單一工作來設定持續部署。 下列 YAML 檔會建立[Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops)工作：

```yaml
pool:
  name: Hosted Windows 2019 with VS2019
  demands: azureps

steps:
- task: AzurePowerShell@3
  inputs:
    azureSubscription: 'demo-deploy-sp'
    ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
    ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus'
    azurePowerShellVersion: LatestVersion
```

當您將工作設定為時 `AzurePowerShell@3` ，管線會使用來自 AzureRM 模組的命令來驗證連接。 根據預設，Visual Studio 專案中的 PowerShell 腳本會使用 AzureRM 模組。 如果您已將腳本更新為使用[Az 模組](/powershell/azure/new-azureps-module-az)，請將工作設定為 `AzurePowerShell@4` 。

```yaml
steps:
- task: AzurePowerShell@4
```

針對 `azureSubscription` ，請提供您所建立之服務連線的名稱。

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

針對 `scriptPath` ，請提供從管線檔案到您腳本的相對路徑。 您可以查看您的存放庫，以查看路徑。

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

如果您不需要預備成品，只要傳遞資源群組的名稱和位置，即可用於部署。 Visual Studio 腳本會建立資源群組（如果尚未存在的話）。

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

如果您需要將成品暫存到現有的儲存體帳戶，請使用：

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

現在，您已瞭解如何建立工作，讓我們逐步完成編輯管線的步驟。

1. 開啟您的管線，並將內容取代為您的 YAML：

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019
     demands: azureps

   steps:
   - task: AzurePowerShell@3
     inputs:
       azureSubscription: 'demo-deploy-sp'
       ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
       ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName 'stage3a4176e058d34bb88cc'
       azurePowerShellVersion: LatestVersion
   ```

1. 選取 [儲存]。

   ![儲存管線](./media/add-template-to-azure-pipelines/save-pipeline.png)

1. 提供認可的訊息，並直接認可到**master**。

1. 當您選取 [**儲存**] 時，會自動執行組建管線。 回到組建管線的 [摘要]，然後監看狀態。

   ![檢視結果](./media/add-template-to-azure-pipelines/view-results.png)

您可以選取目前正在執行的管線，以查看工作的詳細資料。 當它完成時，您會看到每個步驟的結果。

## <a name="copy-and-deploy-tasks"></a>複製和部署工作

本節說明如何使用兩項工作來設定持續部署，以預備成品並部署範本。

下列 YAML 顯示「 [Azure 檔案複製](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)」工作：

```yaml
- task: AzureFileCopy@3
  displayName: 'Stage files'
  inputs:
    SourcePath: 'AzureResourceGroup1'
    azureSubscription: 'demo-deploy-sp'
    Destination: 'AzureBlob'
    storage: 'stage3a4176e058d34bb88cc'
    ContainerName: 'democontainer'
    outputStorageUri: 'artifactsLocation'
    outputStorageContainerSasToken: 'artifactsLocationSasToken'
    sasTokenTimeOutInMinutes: '240'
```

此工作有數個部分可針對您的環境進行修訂。 `SourcePath`表示成品相對於管線檔案的位置。 在此範例中，檔案存在於名為的資料夾中， `AzureResourceGroup1` 其為專案的名稱。

```yaml
SourcePath: '<path-to-artifacts>'
```

針對 `azureSubscription` ，請提供您所建立之服務連線的名稱。

```yaml
azureSubscription: '<your-connection-name>'
```

針對 [儲存體] 和 [容器名稱]，提供您要用來儲存構件的儲存體帳戶和容器名稱。 儲存體帳戶必須存在。

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

下列 YAML 會顯示[Azure Resource Manager 範本部署](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)工作：

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    deploymentScope: 'Resource Group'
    ConnectedServiceName: 'demo-deploy-sp'
    subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'Central US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
    deploymentMode: 'Incremental'
```

此工作有數個部分可針對您的環境進行修訂。

- `deploymentScope`：從選項中選取部署的範圍： `Management Group` 、 `Subscription` 和 `Resource Group` 。 在本逐步解說中使用**資源群組**。 若要深入了解範圍，請參閱[部署範圍](deploy-rest.md#deployment-scope)。

- `ConnectedServiceName`：提供您所建立之服務連線的名稱。

    ```yaml
    ConnectedServiceName: '<your-connection-name>'
    ```

- `subscriptionName`：提供目標訂用帳戶識別碼。 此屬性僅適用于資源群組部署範圍和訂用帳戶部署範圍。

- `resourceGroupName`和 `location` ：提供您想要部署之資源群組的名稱和位置。 此工作會建立資源群組（如果不存在）。

    ```yaml
    resourceGroupName: '<resource-group-name>'
    location: '<location>'
    ```

部署工作會連結至名為的範本 `WebSite.json` ，以及名為 WebSite.parameters.js的參數檔案。 使用範本和參數檔案的名稱。

現在，您已瞭解如何建立工作，讓我們逐步完成編輯管線的步驟。

1. 開啟您的管線，並將內容取代為您的 YAML：

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019

   steps:
   - task: AzureFileCopy@3
     displayName: 'Stage files'
     inputs:
       SourcePath: 'AzureResourceGroup1'
       azureSubscription: 'demo-deploy-sp'
       Destination: 'AzureBlob'
       storage: 'stage3a4176e058d34bb88cc'
       ContainerName: 'democontainer'
       outputStorageUri: 'artifactsLocation'
       outputStorageContainerSasToken: 'artifactsLocationSasToken'
       sasTokenTimeOutInMinutes: '240'
    - task: AzureResourceGroupDeployment@2
      displayName: 'Deploy template'
      inputs:
        deploymentScope: 'Resource Group'
        ConnectedServiceName: 'demo-deploy-sp'
        subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
        action: 'Create Or Update Resource Group'
        resourceGroupName: 'demogroup'
        location: 'Central US'
        templateLocation: 'URL of the file'
        csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
        csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
        overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
        deploymentMode: 'Incremental'
   ```

1. 選取 [儲存]。

1. 提供認可的訊息，並直接認可到**master**。

1. 當您選取 [**儲存**] 時，會自動執行組建管線。 回到組建管線的 [摘要]，然後監看狀態。

   ![檢視結果](./media/add-template-to-azure-pipelines/view-results.png)

您可以選取目前正在執行的管線，以查看工作的詳細資料。 當它完成時，您會看到每個步驟的結果。

## <a name="next-steps"></a>後續步驟

如需有關使用 Azure Pipelines 搭配 ARM 範本的逐步程式，請參閱[教學課程： Azure Resource Manager 範本與 Azure Pipelines 的持續整合](deployment-tutorial-pipeline.md)。
