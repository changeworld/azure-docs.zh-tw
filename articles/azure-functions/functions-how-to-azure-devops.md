---
title: 使用 Azure DevOps 持續更新函式應用程式程式碼
description: 瞭解如何設定以 Azure Functions 為目標的 Azure DevOps 管線。
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurecli
ms.openlocfilehash: d503d71cf44446f93fab3d292d4c26d9b7b0941d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88210223"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>使用 Azure DevOps 持續傳遞

您可以使用 [Azure Pipelines](/azure/devops/pipelines/)，將函數自動部署到 Azure Functions 應用程式。

您有兩個選項可定義管線：

- **YAML**檔： YAML 檔案會描述管線。 檔案可能會有組建步驟區段和發行區段。 YAML 檔案必須位於與應用程式相同的存放庫中。
- **範本**：範本是現成的工作，可建立或部署您的應用程式。

## <a name="yaml-based-pipeline"></a>以 YAML 為基礎的管線

若要建立以 YAML 為基礎的管線，請先建立您的應用程式，然後再部署應用程式。

### <a name="build-your-app"></a>建置應用程式

在 Azure Pipelines 中建立應用程式的方式取決於您應用程式的程式設計語言。 每種語言都有建立部署成品的特定組建步驟。 部署成品可用來在 Azure 中部署您的函數應用程式。

# <a name="c"></a>[C\#](#tab/csharp)

您可以使用下列範例來建立 YAML 檔案，以建立 .NET 應用程式：

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: false
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

您可以使用下列範例來建立 YAML 檔案，以建立 JavaScript 應用程式：

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="python"></a>[Python](#tab/python)

您可以使用下列其中一個範例來建立 YAML 檔案，以針對特定的 Python 版本建立應用程式。 只有在 Linux 上執行的函式應用程式才支援 Python。

**版本3。7**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.7 as required by functions"
  inputs:
    versionSpec: '3.7'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

**版本3。6**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/python3.6/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

您可以使用下列範例來建立 YAML 檔案，以封裝 PowerShell 應用程式。 只有 Windows Azure Functions 支援 PowerShell。

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

---

### <a name="deploy-your-app"></a>部署您的應用程式

您必須在 YAML 檔案中包含下列其中一個 YAML 範例，視裝載作業系統而定。

#### <a name="windows-function-app"></a>Windows 函數應用程式

您可以使用下列程式碼片段來部署 Windows 函數應用程式：

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>Linux 函數應用程式

您可以使用下列程式碼片段來部署 Linux 函式應用程式：

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>以範本為基礎的管線

Azure DevOps 中的範本是預先定義的工作群組，可建立或部署應用程式。

### <a name="build-your-app"></a>建置應用程式

在 Azure Pipelines 中建立應用程式的方式取決於您應用程式的程式設計語言。 每種語言都有建立部署成品的特定組建步驟。 部署成品可用來在 Azure 中更新您的函數應用程式。

若要使用內建的組建範本，當您建立新的組建管線時，請選取 **[使用傳統編輯器** ] 來使用設計工具範本建立管線。

![選取 Azure Pipelines 傳統編輯器](media/functions-how-to-azure-devops/classic-editor.png)

設定程式碼的來源之後，請搜尋 Azure Functions 組建範本。 選取符合您應用程式語言的範本。

![選取 Azure Functions 組建範本](media/functions-how-to-azure-devops/build-templates.png)

在某些情況下，組建構件會有特定的資料夾結構。 您可能需要選取 [封存 **路徑的前置根資料夾名稱** ] 核取方塊。

![要在根資料夾名稱前面加上的選項](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript 應用程式

如果您的 JavaScript 應用程式相依于 Windows 原生模組，您必須將代理程式組件區版本更新為 **HOSTED VS2017**。

![更新代理程式組件區版本](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>部署您的應用程式

當您建立新的發行管線時，請搜尋 Azure Functions 版的範本。

![搜尋 Azure Functions 發行範本](media/functions-how-to-azure-devops/release-template.png)

發行範本不支援部署至部署位置。

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>使用 Azure CLI 建立組建管線

若要在 Azure 中建立組建管線，請使用 `az functionapp devops-pipeline create` [命令](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create)。 建立組建管線，以建立併發行您存放庫中所做的任何程式碼變更。 此命令會產生新的 YAML 檔案，該檔案會定義組建和發行管線，然後將其認可至您的存放庫。 此命令的必要條件取決於程式碼的位置。

- 如果您的程式碼位於 GitHub：

    - 您必須具有訂用帳戶的 **寫入** 許可權。

    - 您必須是 Azure DevOps 中的專案系統管理員。

    - 您必須擁有建立 GitHub 個人存取權杖的許可權， (PAT) 具有足夠的許可權。 如需詳細資訊，請參閱 [GITHUB PAT 許可權需求。](https://aka.ms/azure-devops-source-repos)

    - 您必須擁有在 GitHub 存放庫中認可至主要分支的許可權，才能認可自動產生的 YAML 檔案。

- 如果您的程式碼位於 Azure Repos：

    - 您必須具有訂用帳戶的 **寫入** 許可權。

    - 您必須是 Azure DevOps 中的專案系統管理員。

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure Functions 總覽](functions-overview.md)。
- 請參閱 [Azure DevOps 總覽](/azure/devops/pipelines/)。
