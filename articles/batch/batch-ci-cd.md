---
title: 使用 Azure 管道生成&部署 HPC 解決方案 - Azure 批次處理
description: 瞭解如何為在 Azure Batch 上運行的 HPC 應用程式部署生成/發佈管道。
author: chrisreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.service: batch
ms.openlocfilehash: 50cb711dfd16c2a8718d13ba9255ace1e7e3e26d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533125"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>使用 Azure 管道構建和部署 HPC 解決方案

Azure DevOps 服務提供開發團隊在構建自訂應用程式時使用的一系列工具。 Azure DevOps 提供的工具可以轉換為高性能計算解決方案的自動構建和測試。 本文演示如何使用 Azure 管道為 Azure Batch 上部署的高性能計算解決方案設置連續集成 （CI） 和連續部署 （CD）。

Azure 管道提供了一系列用於構建、部署、測試和監視軟體的現代 CI/CD 流程。 這些過程可加快軟體交付速度，使您能夠專注于代碼，而不是支援基礎結構和操作。

## <a name="create-an-azure-pipeline"></a>創建 Azure 管道

在此示例中，我們將創建一個生成和發佈管道來部署 Azure Batch 基礎結構併發布應用程式包。 假設代碼是在本地開發的，這是常規部署流：

![顯示管道中部署流的圖表](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>安裝程式

要按照本文中的步驟操作，您需要 Azure DevOps 組織和團隊專案。

* [創建 Azure 開發人員組織](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [在 Azure 開發人員計畫中創建專案](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>環境的原始程式碼管理

原始程式碼管理允許團隊跟蹤對代碼庫所做的更改，並檢查代碼的早期版本。

通常，原始程式碼控制被認為是與軟體代碼攜手並進。 底層基礎架構如何？ 這讓我們將基礎結構作為代碼，我們將使用 Azure 資源管理器範本或其他開源替代方法來聲明定義我們的底層基礎結構。

此示例嚴重依賴許多資源管理器範本 （JSON 文檔） 和現有的二進位檔案。 您可以將這些示例複製到存儲庫中，並將它們推送到 Azure DevOps。

此示例中使用的代碼庫結構類似于以下內容;

* 包含許多 Azure 資源管理器範本的**臂部範本**資料夾。 本文將介紹這些範本。
* **用戶端應用程式**資料夾，它是[Azure Batch .NET 檔處理的副本，包含 ffmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial)示例。 本文不需要這樣做。
* **hpc 應用程式**資料夾，它是[ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)的 Windows 64 位版本。
* **管道**資料夾。 其中包含一個 YAML 檔，概述了我們的生成過程。 本文將討論這一點。

本節假定您熟悉版本控制和設計資源管理器範本。 如果您不熟悉這些概念，請參閱以下頁面瞭解更多資訊。

* [什麼是原始程式碼管理？](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [瞭解 Azure 資源管理器範本的結構和語法](../azure-resource-manager/templates/template-syntax.md)

#### <a name="azure-resource-manager-templates"></a>Azure 資源管理員範本

此示例利用多個資源管理器範本來部署我們的解決方案。 為此，我們使用許多實現特定功能的功能功能（類似于單元或模組）的功能範本。 我們還使用端到端解決方案範本，該範本負責將這些底層功能整合在一起。 這種方法有幾個好處：

* 基礎功能範本可以單獨進行單元測試。
* 基礎功能範本可以定義為組織內部的標準，並在多個解決方案中重用。

在此示例中，有一個端到端解決方案範本 （deployment.json） 部署三個範本。 基礎範本是功能範本，負責部署解決方案的特定方面。

![使用 Azure 資源管理器範本連結範本結構的示例](media/batch-ci-cd/ARMTemplateHierarchy.png)

我們將查看的第一個範本是 Azure 存儲帳戶。 我們的解決方案需要存儲帳戶才能將應用程式部署到我們的批次處理帳戶。 在為存儲帳戶構建資源管理器範本時，值得了解[Microsoft.存儲資源類型的資源管理器範本參考指南](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions)。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

接下來，我們將查看 Azure 批次處理帳戶範本。 Azure 批次處理帳戶充當跨池（電腦分組）運行大量應用程式的平臺。 在為批次處理帳戶構建資源管理器範本時，值得了解 Microsoft 的[資源管理器範本參考指南](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions)。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

下一個範本顯示了創建 Azure 批次處理池的示例（用於處理應用程式的後端電腦）。 在為批次處理帳戶池構建資源管理器範本時，值得了解 Microsoft 的[資源管理器範本參考指南](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions)。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

最後，我們有一個類似于協調器的範本。 此範本負責部署功能範本。

您還可以在單獨的文章中瞭解有關[創建連結的 Azure 資源管理器範本](../azure-resource-manager/templates/template-tutorial-create-linked-templates.md)的更多資訊。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resouce Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resouce Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

#### <a name="the-hpc-solution"></a>HPC 解決方案

基礎結構和軟體可以定義為代碼，並位於同一存儲庫中。

對於此解決方案，ffmpeg 用作應用程式包。 ffmpeg 套裝軟體可以[在這裡](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)下載。

![示例 Git 存儲庫結構](media/batch-ci-cd/git-repository.jpg)

此存儲庫有四個主要部分：

* 將基礎結構存儲為代碼**的手臂範本**資料夾
* 包含 ffmpeg 二進位檔案的**hpc 應用程式**資料夾
* 包含生成**管道定義的管道**資料夾。
* **可選**：將存儲 .NET 應用程式代碼的**用戶端應用程式**資料夾。 我們不會在示例中使用，但在您自己的專案中，您可能希望通過用戶端應用程式執行 HPC 批次處理應用程式的運行。

> [!NOTE]
> 這只是代碼庫結構的一個示例。 此方法用於演示應用程式、基礎結構和管道代碼存儲在同一存儲庫中的目的。

現在，原始程式碼已經設置，我們可以開始第一個生成。

## <a name="continuous-integration"></a>持續整合

[Azure 管道](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops)（在 Azure DevOps 服務中） 可説明您為應用程式實現生成、測試和部署管道。

在管道的此階段，通常會運行測試以驗證代碼並構建軟體的適當部分。 測試的數量和類型以及運行的任何其他任務將取決於更廣泛的生成和發佈策略。

## <a name="preparing-the-hpc-application"></a>準備 HPC 應用程式

在此示例中，我們將重點介紹**hpc 應用程式**資料夾。 **hpc 應用程式**資料夾是從 Azure Batch 帳戶中運行的 ffmpeg 軟體。

1. 導航到 Azure DevOps 組織中 Azure 管道的生成部分。 創建新**管道**。

    ![創建新生成管道](media/batch-ci-cd/new-build-pipeline.jpg)

1. 創建生成管道有兩個選項：

    a. [使用視覺化設計器](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav)。 要使用此，請按一下 **"新建管道**"頁上的"使用視覺化設計器"。

    b. [使用 YAML 生成](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops)。 您可以通過按一下"新建管道"頁上的 Azure Repos 或 GitHub 選項創建新的 YAML 管道。 或者，您可以通過按一下 Visual 設計器，然後使用 YAML 範本，將下面的示例存儲在原始程式碼管理中並引用現有的 YAML 檔。

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. 根據需要配置生成後，選擇 **"保存&佇列**"。 如果啟用了連續集成（在**觸發器**部分中），則生成將在對存儲庫進行新提交時自動觸發，從而滿足生成中設置的條件。

    ![現有生成管道的示例](media/batch-ci-cd/existing-build-pipeline.jpg)

1. 通過導航到 Azure 管道的 **"生成"** 部分，在 Azure DevOps 中查看生成進度的即時更新。 從組建定義中選擇適當的生成。

    ![查看生成的即時輸出](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> 如果使用用戶端應用程式執行 HPC 批次處理應用程式，則需要為該應用程式創建單獨的組建定義。 您可以在[Azure 管道](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops)文檔中找到許多操作指南。

## <a name="continuous-deployment"></a>連續部署

Azure 管道還用於部署應用程式和基礎基礎結構。 [發佈管道](https://docs.microsoft.com/azure/devops/pipelines/release)是支援持續部署並自動執行發佈過程的元件。

### <a name="deploying-your-application-and-underlying-infrastructure"></a>部署應用程式和底層基礎結構

部署基礎結構涉及許多步驟。 由於我們使用[連結範本](../azure-resource-manager/templates/linked-templates.md)，這些範本需要從公共終結點 （HTTP 或 HTTPS） 訪問。 這可能是 GitHub 上的存儲庫、Azure Blob 存儲帳戶或其他存儲位置。 上載的範本專案可以保持安全，因為它們可以在私有模式下保留，但使用某種形式的共用訪問簽名 （SAS） 權杖進行訪問。 下面的示例演示如何使用 Azure 存儲 Blob 中的範本部署基礎結構。

1. 創建**新版本定義**，然後選擇空定義。 然後，我們需要將新創建的環境重命名為與管道相關的環境。

    ![初始發佈管道](media/batch-ci-cd/Release-0.jpg)

1. 創建對生成管道的依賴項，以獲得 HPC 應用程式的輸出。

    > [!NOTE]
    > 再次，請注意**源別名**，因為當在發佈定義中創建任務時，將需要這樣做。

    ![在適當的生成管道中創建到 HPC 應用程式包的專案連結](media/batch-ci-cd/Release-1.jpg)

1. 創建指向另一個專案的連結，這次是 Azure 存儲庫。 這是訪問存儲庫中存儲的資源管理器範本所必需的。 由於資源管理器範本不需要編譯，因此無需通過生成管道推送它們。

    > [!NOTE]
    > 再次，請注意**源別名**，因為當在發佈定義中創建任務時，將需要這樣做。

    ![創建到 Azure 存儲庫的專案連結](media/batch-ci-cd/Release-2.jpg)

1. 導航到**變數**部分。 建議在管道中創建多個變數，因此您不會將相同的資訊輸入多個任務。 這些是本示例中使用的變數，以及它們如何影響部署。

    * **應用程式存儲帳戶名稱**：存儲帳戶的名稱，以保存 HPC 應用程式二進位檔案
    * **批次處理帳戶應用程式名稱**：Azure 批次處理帳戶中的應用程式名稱
    * **批次處理帳戶名稱**：Azure 批次處理帳戶的名稱
    * **批次處理帳戶池名稱**：執行處理的 VM 池的名稱
    * **批次處理應用程式 Id**：Azure 批次處理應用程式的唯一 ID
    * **批次處理應用程式版本**：批次處理應用程式的語義版本（即 ffmpeg 二進位檔案）
    * **位置**：要部署的 Azure 資源的位置
    * **資源組名稱**：要創建的資源組的名稱，以及資源的部署位置
    * **存儲帳戶名稱**：存儲帳戶的名稱，用於保存連結的資源管理器範本

    ![為 Azure 管道版本設置的變數示例](media/batch-ci-cd/Release-4.jpg)

1. 導航到開發人員環境的任務。 在下面的快照中，您可以看到六個任務。 這些任務將：下載壓縮的 ffmpeg 檔、部署存儲帳戶以承載嵌套資源管理器範本、將這些資源管理器範本複製到存儲帳戶、部署批次處理帳戶和所需的依賴項、在Azure 批次處理帳戶並將應用程式包上載到 Azure 批次處理帳戶。

    ![用於將 HPC 應用程式發佈到 Azure 批次處理的任務示例](media/batch-ci-cd/Release-3.jpg)

1. 添加**下載管道專案（預覽）** 任務並設置以下屬性：
    * **顯示名稱：** 將應用程式包下載到代理
    * **要下載的專案的名稱：hpc**應用程式
    * **下載路徑 ：**$（系統.預設工作目錄）

1. 創建存儲帳戶以存儲您的專案。 可以使用解決方案的現有存儲帳戶，但對於獨立示例和內容隔離，我們正在為我們的專案（特別是資源管理器範本）創建專用存儲帳戶。

    添加**Azure 資源組部署**任務並設置以下屬性：
    * **顯示名稱：** 為資源管理器範本部署存儲帳戶
    * **Azure 訂閱：** 選擇相應的 Azure 訂閱
    * **操作**：創建或更新資源組
    * **資源組**： $（資源組名稱）
    * **地點**： $（位置）
    * **範本**： $（系統.artifactsDirectory）/**[您的 Azure RepoeeesourceAlias]**/arm 範本/存儲帳戶.json
    * **覆蓋範本參數**： -帳戶名稱 $（存儲帳戶名稱）

1. 將專案從源控制項上載到存儲帳戶。 有一個 Azure 管道任務可以執行此操作。 作為此任務的一部分，可以將存儲帳戶容器 URL 和 SAS 權杖輸出到 Azure 管道中的變數。 這意味著它可以在此代理階段重複使用。

    添加**Azure 檔案複製**任務並設置以下屬性：
    * **來源：** $（系統.artifactsDirectory）/**[您的 Azure RepoeeesourceAlias]**/arm 範本/
    * **Azure 連線類型**： Azure 資源管理器
    * **Azure 訂閱：** 選擇相應的 Azure 訂閱
    * **目標型別**： Azure Blob
    * **RM 存儲帳戶**： $（存儲帳戶名稱）
    * **容器名稱**：範本
    * **存儲容器 URI**：範本容器 URI
    * **存儲容器 SAS 權杖**：範本容器 SasToken

1. 部署協調器範本。 從前面回顧協調器範本，您會注意到除了 SAS 權杖之外，存儲帳戶容器 URL 還有參數。 您應該注意到，資源管理器範本中所需的變數要麼位於發佈定義的變數部分，要麼從另一個 Azure 管道任務（例如，Azure Blob 複製任務的一部分）設置。

    添加**Azure 資源組部署**任務並設置以下屬性：
    * **顯示名稱：** 部署 Azure 批次處理
    * **Azure 訂閱：** 選擇相應的 Azure 訂閱
    * **操作**：創建或更新資源組
    * **資源組**： $（資源組名稱）
    * **地點**： $（位置）
    * **範本**： $（系統.artifactsDirectory）/**[您的 Azure RepoeeesourceAlias]**/arm 範本/部署.json
    * **覆蓋範本參數**：```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

常見做法是使用 Azure 金鑰保存庫任務。 如果服務主體（連接到 Azure 訂閱）具有適當的訪問策略集，則可以從 Azure 金鑰保存庫下載機密，並將其用作管道中的變數。 機密的名稱將與關聯的值一起設置。 例如，可以在發佈定義中引用 sshPassword 的機密和 $（sshPassword）。

1. 後續步驟調用 Azure CLI。 第一個用於在 Azure 批次處理中創建應用程式。 並上傳關聯的包。

    添加**Azure CLI**任務並設置以下屬性：
    * **顯示名稱：** 在 Azure 批次處理帳戶中創建應用程式
    * **Azure 訂閱：** 選擇相應的 Azure 訂閱
    * **腳本位置**： 內聯腳本
    * **內聯腳本**：```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. 第二步用於將關聯的包上載到應用程式。 在我們的例子中，ffmpeg 檔。

    添加**Azure CLI**任務並設置以下屬性：
    * **顯示名稱：** 將包上載到 Azure 批次處理帳戶
    * **Azure 訂閱：** 選擇相應的 Azure 訂閱
    * **腳本位置**： 內聯腳本
    * **內聯腳本**：```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > 應用程式包的版本號設置為變數。 如果覆蓋包的早期版本適用于您，並且想要手動控制推送到 Azure Batch 的包的版本號，則此功能非常方便。

1. 通過選擇 **"發佈>創建新版本"來創建新版本**。 觸發後，選擇指向新版本的連結以查看狀態。

1. 您可以通過選擇環境下方的 **"日誌"** 按鈕來查看來自代理的即時輸出。

    ![查看發佈狀態](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>測試環境

設置環境後，確認以下測試可以成功完成。

使用 PowerShell 命令提示符中的 Azure CLI 連接到新的 Azure 批次處理帳戶。

* 使用 登錄到 Azure 帳戶`az login`並按照說明進行身份驗證。
* 現在對批次處理帳戶進行身份驗證：`az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>列出可用的應用程式

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>檢查池是否有效

```azurecli
az batch pool list
```

請注意`currentDedicatedNodes`此命令的輸出中的值。 此值在下一次測試中調整。

#### <a name="resize-the-pool"></a>調整池大小

調整池大小，以便有可用於作業和任務測試的計算節點，請與池清單命令一起查看目前狀態，直到調整大小完成並且有可用的節點

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>後續步驟

除了本文之外，還有兩個教程使用 ffmpeg，使用 .NET 和 Python。 有關如何通過簡單應用程式與 Batch 帳戶進行交互的詳細資訊，請參閱這些教程。

* [使用 Python API 透過 Azure Batch 執行平行工作負載](tutorial-parallel-python.md)
* [使用 .NET API 透過 Azure Batch 執行平行工作負載](tutorial-parallel-dotnet.md)
