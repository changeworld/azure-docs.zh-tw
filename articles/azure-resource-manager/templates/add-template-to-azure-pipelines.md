---
title: 具有 Azure Pipelines 和範本的 CI/CD
description: 說明如何使用 Azure Resource Manager 範本，在 Azure Pipelines 中設定持續整合。 它會顯示如何使用 PowerShell 腳本，或將檔案複製到預備位置，並從該處部署。
ms.topic: conceptual
ms.date: 10/01/2020
ms.openlocfilehash: 6784df30340e4c54b8b1d6e82b45046666824315
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653395"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>整合 ARM 範本與 Azure Pipelines

您可以使用 Azure Pipelines 進行持續整合和持續部署 (CI/CD) ，來整合 Azure Resource Manager 範本 (ARM 範本) 。 搭配 [Azure Pipelines 的 arm 範本持續整合](deployment-tutorial-pipeline.md) 教學課程會示範如何使用 [arm 範本部署](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md) 工作，從 GitHub 存放庫部署範本。 當您想要直接從存放庫部署範本時，此方法適用。

在本文中，您將瞭解使用 Azure Pipelines 部署範本的兩種方法。 本文將說明如何：

* **新增執行 Azure PowerShell 腳本的**工作。 此選項的優點是在整個開發生命週期中提供一致性，因為您可以使用執行本機測試時所使用的相同腳本。 您的腳本會部署範本，但也可以執行其他作業，例如取得要做為參數的值。

   Visual Studio 提供包含 PowerShell 腳本的 [Azure 資源群組專案](create-visual-studio-deployment-project.md) 。 腳本會將專案中的成品構件至 Resource Manager 可以存取的儲存體帳戶。 構件是您專案中的專案，例如連結的範本、腳本和應用程式二進位檔。 如果您想要繼續使用專案中的腳本，請使用本文中所示的 PowerShell 腳本工作。

* **新增工作以複製及部署**工作。 此選項提供專案腳本的便利替代方案。 您可以在管線中設定兩項工作。 一個工作會將成品階段到可存取的位置。 另一個工作會從該位置部署範本。

## <a name="prepare-your-project"></a>準備您的專案

本文假設您的 ARM 範本和 Azure DevOps 組織已準備好建立管線。 下列步驟說明如何確定您已經準備就緒：

* 您有 Azure DevOps 的組織。 如果您沒有帳戶，請 [建立一個免費的](/azure/devops/pipelines/get-started/pipelines-sign-up)帳戶。 如果您的小組已經有 Azure DevOps 組織，請確定您是要使用之 Azure DevOps 專案的系統管理員。

* 您已設定 Azure 訂用帳戶的 [服務連接](/azure/devops/pipelines/library/connect-to-azure) 。 管線中的工作會在服務主體的身分識別下執行。 如需建立連接的步驟，請參閱 [建立 DevOps 專案](deployment-tutorial-pipeline.md#create-a-devops-project)。

* 您有一個 [ARM 範本](quickstart-create-templates-use-visual-studio-code.md) ，可定義專案的基礎結構。

## <a name="create-pipeline"></a>建立管線

1. 如果您先前尚未新增管線，則必須建立新的管線。 從您的 Azure DevOps 組織中，選取 [ **管線** ] 和 [ **新增管線**]。

   ![新增管線](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. 指定您的程式碼儲存位置。 下圖顯示如何選取 **Azure Repos Git**。

   ![選取程式碼來源](./media/add-template-to-azure-pipelines/select-source.png)

1. 從該來源選取具有您專案程式碼的存放庫。

   ![選取存放庫](./media/add-template-to-azure-pipelines/select-repo.png)

1. 選取要建立的管線類型。 您可以選取 [ **入門管線**]。

   ![選取管線](./media/add-template-to-azure-pipelines/select-pipeline.png)

您已經準備好新增 Azure PowerShell 工作或複製檔案，以及部署工作。

## <a name="azure-powershell-task"></a>Azure PowerShell 工作

本節說明如何使用在專案中執行 PowerShell 腳本的單一工作來設定持續部署。 如果您需要部署範本的 PowerShell 腳本，請參閱 [Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) 或 [Deploy-AzureResourceGroup.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzureResourceGroup.ps1)。

下列 YAML 檔會建立 [Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell)工作：

```yml
trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: AzurePowerShell@5
  inputs:
    azureSubscription: 'script-connection'
    ScriptType: 'FilePath'
    ScriptPath: './Deploy-Template.ps1'
    ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
    azurePowerShellVersion: 'LatestVersion'
```

當您將工作設定為時 `AzurePowerShell@5` ，管線會使用 [Az 模組](/powershell/azure/new-azureps-module-az)。 如果您是在腳本中使用 AzureRM 模組，請將工作設定為 `AzurePowerShell@3` 。

```yaml
steps:
- task: AzurePowerShell@3
```

針對 `azureSubscription` ，提供您所建立的服務連線名稱。

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

針對 `scriptPath` ，提供從管線檔案到腳本的相對路徑。 您可以查看存放庫以查看路徑。

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

在中 `ScriptArguments` ，提供腳本所需的任何參數。 下列範例顯示腳本的一些參數，但您必須自訂腳本的參數。

```yaml
ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
```

當您選取 [ **儲存**] 時，組建管線會自動執行。 返回至組建管線的摘要，並監看狀態。

![檢視結果](./media/add-template-to-azure-pipelines/view-results.png)

您可以選取目前正在執行的管線，以查看工作的詳細資料。 完成時，您會看到每個步驟的結果。

## <a name="copy-and-deploy-tasks"></a>複製和部署工作

本節說明如何使用兩個工作來設定持續部署。 第一個工作會將成品階段到儲存體帳戶，第二個工作則是部署範本。

若要將檔案複製到儲存體帳戶，必須將儲存體 Blob 資料參與者或儲存體 Blob 資料擁有者角色指派給服務連接的服務主體。 如需詳細資訊，請參閱 [開始使用 AzCopy](../../storage/common/storage-use-azcopy-v10.md)。

下列 YAML 顯示 Azure 檔案 [複製](/azure/devops/pipelines/tasks/deploy/azure-file-copy)工作。

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
```

這項工作有幾個部分可針對您的環境進行修訂。 `SourcePath`指出成品相對於管線檔案的位置。

```yaml
SourcePath: '<path-to-artifacts>'
```

針對 `azureSubscription` ，提供您所建立的服務連線名稱。

```yaml
azureSubscription: '<your-connection-name>'
```

針對儲存體和容器名稱，提供您要用來儲存構件的儲存體帳戶和容器的名稱。 儲存體帳戶必須存在。

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

建立複製檔案工作之後，您就可以新增工作來部署暫存範本。

下列 YAML 顯示 [Azure Resource Manager 範本部署](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)工作：

```yaml
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

這項工作有幾個部分需要更詳細的評論。

- `deploymentScope`：從選項中選取部署的範圍： `Management Group` 、 `Subscription` 和 `Resource Group` 。 若要深入了解範圍，請參閱[部署範圍](deploy-rest.md#deployment-scope)。

- `azureResourceManagerConnection`：提供您所建立之服務連接的名稱。

- `subscriptionId`：提供目標訂用帳戶識別碼。 此屬性只適用于資源群組部署範圍和訂用帳戶部署範圍。

- `resourceGroupName` 和 `location` ：提供您要部署之資源群組的名稱和位置。 此工作會建立資源群組（如果不存在的話）。

   ```yml
   resourceGroupName: '<resource-group-name>'
   location: '<location>'
   ```

- `csmFileLink`：提供分段範本的連結。 設定值時，請使用檔案複製工作所傳回的變數。 下列範例會連結至名為 mainTemplate.js的範本。 包含名為 **templates** 的資料夾是因為檔案複製工作將檔案複製到其中。 在您的管線中，提供範本的路徑和範本的名稱。

   ```yml
   csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
   ```

您的管線看起來像這樣：

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

當您選取 [ **儲存**] 時，組建管線會自動執行。 返回至組建管線的摘要，並監看狀態。

## <a name="next-steps"></a>後續步驟

若要瞭解如何搭配 GitHub Actions 使用 ARM 範本，請參閱 [使用 GitHub Actions 部署 Azure Resource Manager 範本](deploy-github-actions.md)。
