---
title: 使用 Azure DevOps 不斷更新函數應用代碼
description: 瞭解如何設置以 Azure 函數為目標的 Azure DevOps 管道。
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.openlocfilehash: 5e2fc8fb06248e2cdad9067c56647da6d9626b50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255762"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>使用 Azure DevOps 連續傳遞

可以使用[Azure 管道](/azure/devops/pipelines/)自動將函數部署到 Azure 函數應用。

您有兩個選項用於定義管道：

- **YAML 檔**：YAML 檔描述管道。 該檔可能具有生成步驟部分和發佈部分。 YAML 檔必須與應用處于同一回購中。
- **範本**：範本是生成或部署應用的現成任務。

## <a name="yaml-based-pipeline"></a>基於 YAML 的管道

要創建基於 YAML 的管道，請先生成應用，然後部署應用。

### <a name="build-your-app"></a>建置您的應用程式

如何在 Azure 管道中構建應用取決於應用的程式設計語言。 每種語言都有創建部署專案的特定生成步驟。 部署專案用於在 Azure 中部署函數應用。

# <a name="c"></a>[C\#](#tab/csharp)

可以使用以下示例創建 YAML 檔來生成 .NET 應用：

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

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

可以使用以下示例創建 YAML 檔來構建 JavaScript 應用：

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

可以使用以下示例之一創建 YAML 檔，為特定的 Python 版本構建應用。 Python 僅支援在 Linux 上運行的函數應用程式。

**版本 3.7**

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

**版本 3.6**

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

# <a name="powershell"></a>[電源外殼](#tab/powershell)

您可以使用以下示例創建 YAML 檔來打包 PowerShell 應用。 僅支援 Windows Azure 功能的 PowerShell。

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

您必須在 YAML 檔中包括以下 YAML 示例之一，具體取決於託管作業系統。

#### <a name="windows-function-app"></a>視窗功能應用

您可以使用以下程式碼片段部署 Windows 函數應用：

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

#### <a name="linux-function-app"></a>Linux函數應用程式

您可以使用以下程式碼片段部署 Linux 函數應用：

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

## <a name="template-based-pipeline"></a>基於範本的管道

Azure DevOps 中的範本是生成或部署應用的任務的預定義組。

### <a name="build-your-app"></a>建置您的應用程式

如何在 Azure 管道中構建應用取決於應用的程式設計語言。 每種語言都有創建部署專案的特定生成步驟。 部署專案用於在 Azure 中更新函數應用。

要使用內置生成範本，請在創建新生成管道時選擇 **"使用經典編輯器**使用設計器範本創建管道"。

![選擇 Azure 管道經典編輯器](media/functions-how-to-azure-devops/classic-editor.png)

配置代碼源後，搜索 Azure 函數生成範本。 選擇與您的應用語言匹配的範本。

![選擇 Azure 函數生成範本](media/functions-how-to-azure-devops/build-templates.png)

在某些情況下，生成工件具有特定的資料夾結構。 您可能需要選擇 **"準備結束根資料夾名稱以存檔路徑**"核取方塊。

![準備根資料夾名稱的選項](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript 應用

如果您的 JavaScript 應用依賴于 Windows 本機模組，則必須將代理池版本更新為**託管 VS2017**。

![更新代理池版本](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>部署您的應用程式

創建新發佈管道時，搜索 Azure 函數發佈範本。

![搜索 Azure 函數發佈範本](media/functions-how-to-azure-devops/release-template.png)

發佈範本不支援部署到部署槽。

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>使用 Azure CLI 創建生成管道

要在 Azure 中創建生成管道，`az functionapp devops-pipeline create`請使用[命令](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create)。 創建生成管道是為了生成和釋放在回購中所做的任何代碼更改。 該命令生成一個新的 YAML 檔，該檔定義生成和發佈管道，然後將其提交到您的回購中。 此命令的先決條件取決於代碼的位置。

- 如果代碼在 GitHub 中：

    - 您必須具有訂閱的**寫入**許可權。

    - 您必須是 Azure DevOps 中的專案管理員。

    - 您必須具有創建具有足夠許可權的 GitHub 個人訪問權杖 （PAT） 的許可權。 有關詳細資訊，請參閱[GitHub PAT 許可權要求。](https://aka.ms/azure-devops-source-repos)

    - 您必須具有向 GitHub 存儲庫中的主分支提交的許可權，以便提交自動生成的 YAML 檔。

- 如果代碼位於 Azure 存儲庫中：

    - 您必須具有訂閱的**寫入**許可權。

    - 您必須是 Azure DevOps 中的專案管理員。

## <a name="next-steps"></a>後續步驟

- 查看[Azure 函數概述](functions-overview.md)。
- 查看[Azure DevOps 概述](/azure/devops/pipelines/)。
