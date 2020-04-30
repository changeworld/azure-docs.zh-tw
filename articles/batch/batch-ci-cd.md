---
title: 使用 Azure Pipelines 建立 & 部署 HPC 解決方案
description: 瞭解如何部署在 Azure Batch 上執行之 HPC 應用程式的組建/發行管線。
author: chrisreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.openlocfilehash: 79c4e74086acc0f74bcc43f6b4543afe12916364
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82117381"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>使用 Azure Pipelines 來建立和部署 HPC 解決方案

Azure DevOps Services 提供開發小組在建立自訂應用程式時所使用的各種工具。 Azure DevOps 所提供的工具可以轉譯為高效能計算解決方案的自動化建立和測試。 本文示範如何使用 Azure Pipelines，針對部署在 Azure Batch 上的高效能計算解決方案，設定持續整合（CI）和持續部署（CD）。

Azure Pipelines 提供各種新式 CI/CD 程式來建立、部署、測試和監視軟體。 這些程式可加速您的軟體交付，讓您可以專注于您的程式碼，而不是支援基礎結構和作業。

## <a name="create-an-azure-pipeline"></a>建立 Azure 管線

在此範例中，我們將建立組建和發行管線，以部署 Azure Batch 基礎結構和發行應用程式封裝。 假設程式碼是在本機開發，這就是一般部署流程：

![此圖顯示管線中的部署流程](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>安裝程式

若要遵循本文中的步驟，您需要 Azure DevOps 組織和 team 專案。

* [建立 Azure DevOps 組織](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [在 Azure DevOps 中建立專案](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>適用于您環境的原始檔控制

原始檔控制可讓小組追蹤對程式碼基底所做的變更，並檢查程式碼的先前版本。

一般來說，原始檔控制會被視為軟體程式碼的手入。 基礎結構如何？ 這會將我們帶入基礎結構即程式碼，我們將使用 Azure Resource Manager 範本或其他開放原始碼替代專案，以宣告方式定義基礎結構。

這個範例會高度依賴許多 Resource Manager 範本（JSON 檔）和現有的二進位檔。 您可以將這些範例複製到您的存放庫，並將它們推送至 Azure DevOps。

此範例中使用的程式碼基底結構與下列類似：

* **Arm 範本**資料夾，其中包含數個 Azure Resource Manager 範本。 本文將說明這些範本。
* **用戶端應用程式**資料夾，這是使用 ffmpeg 範例進行的[Azure Batch .net 檔案處理](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial)的複本。 這不是本文的必要專案。
* **Hpc-應用程式**資料夾，這是 Windows 64 位版本的[ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)。
* [**管線**] 資料夾。 其中包含的 YAML 檔案會概述我們的組建程式。 這會在文章中討論。

本節假設您已熟悉版本控制和設計 Resource Manager 範本。 如果您不熟悉這些概念，請參閱下列頁面以取得詳細資訊。

* [什麼是原始檔控制？](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [瞭解 Azure Resource Manager 範本的結構和語法](../azure-resource-manager/templates/template-syntax.md)

#### <a name="azure-resource-manager-templates"></a>Azure 資源管理員範本

這個範例會利用多個 Resource Manager 範本來部署解決方案。 若要這樣做，我們會使用一些功能範本（類似于單位或模組）來執行特定功能。 我們也會使用端對端解決方案範本，其負責將這些基礎功能結合在一起。 這種方法有幾個優點：

* 基礎功能範本可以個別進行單元測試。
* 基礎功能範本可以定義為組織內部的標準，並可在多個解決方案中重複使用。

在此範例中，有一個可部署三個範本的端對端解決方案範本（deployment. json）。 基礎範本是功能範本，負責部署解決方案的特定層面。

![使用 Azure Resource Manager 範本之連結範本結構的範例](media/batch-ci-cd/ARMTemplateHierarchy.png)

我們會查看的第一個範本是 Azure 儲存體帳戶。 我們的解決方案需要儲存體帳戶，才能將應用程式部署至我們的 Batch 帳戶。 值得注意的是，在建立儲存體帳戶的 Resource Manager 範本時，[適用于 Microsoft 的 Resource Manager 範本參考指南。](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions)

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

接下來，我們將探討 Azure Batch 帳戶範本。 Azure Batch 帳戶可做為跨集區（機器群組）執行許多應用程式的平臺。 值得注意的是，在建立 Batch 帳戶的 Resource Manager 範本時，[適用于 Microsoft batch 資源類型的 Resource Manager 範本參考指南](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions)。

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

下一個範本會顯示建立 Azure Batch 集區（用來處理應用程式的後端機器）的範例。 在建立 Batch 帳戶集區 Resource Manager 範本時，值得注意的是[Microsoft 的 Resource Manager 範本參考指南](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions)。

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

最後，我們有一個類似協調器的範本。 此範本會負責部署功能範本。

您也可以在個別的文章中深入瞭解如何[建立連結的 Azure Resource Manager 範本](../azure-resource-manager/templates/template-tutorial-create-linked-templates.md)。

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

基礎結構和軟體可以定義為程式碼，並在相同的存放庫中共存。

在此解決方案中，會使用 ffmpeg 做為應用程式封裝。 您可以在[這裡](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)下載 ffmpeg 封裝。

![範例 Git 存放庫結構](media/batch-ci-cd/git-repository.jpg)

此存放庫有四個主要區段：

* 將基礎結構儲存為程式碼的**arm 範本**資料夾
* **Hpc-應用程式**資料夾，其中包含 ffmpeg 的二進位檔
* 包含組建管線定義的 [**管線**] 資料夾。
* **選擇性**：會儲存 .net 應用程式程式碼的**用戶端應用程式**資料夾。 我們不會在範例中使用此功能，但在您自己的專案中，您可能想要透過用戶端應用程式執行 HPC 批次應用程式的執行。

> [!NOTE]
> 這只是程式碼基底結構的其中一個範例。 這個方法是用來示範應用程式、基礎結構和管線程式碼會儲存在相同的存放庫中。

現在已設定原始程式碼，我們可以開始第一個組建。

## <a name="continuous-integration"></a>持續整合

[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops)，在 Azure DevOps Services 中，可協助您為應用程式執行組建、測試和部署管線。

在您管線的這個階段中，通常會執行測試來驗證程式代碼，並建立適當的軟體片段。 測試的數目和類型，以及您所執行的任何其他工作，將取決於您更廣泛的組建和發行策略。

## <a name="preparing-the-hpc-application"></a>準備 HPC 應用程式

在此範例中，我們會將焦點放在**hpc-應用程式**資料夾。 **Hpc-應用程式**資料夾是將從 Azure Batch 帳戶內執行的 ffmpeg 軟體。

1. 流覽至 Azure DevOps 組織中 Azure Pipelines 的 [組建] 區段。 建立**新的管線**。

    ![建立新的組建管線](media/batch-ci-cd/new-build-pipeline.jpg)

1. 您有兩個選項可以建立組建管線：

    a. [使用視覺化設計](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav)工具。 若要使用此工具，請按一下 [**新增管線**] 頁面上的 [使用視覺化設計工具]。

    b. [使用 YAML 組建](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops)。 您可以按一下 [新增管線] 頁面上的 [Azure Repos] 或 [GitHub] 選項，以建立新的 YAML 管線。 或者，您可以將下列範例儲存在原始檔控制中，並按一下 [視覺化設計工具] 來參考現有的 YAML 檔，然後使用 [YAML] 範本。

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

1. 視需要設定組建之後，請選取 [**儲存 & 佇列**]。 如果您已啟用持續整合（在 [**觸發**程式] 區段中），則組建會在對存放庫進行新的認可時自動觸發，以符合組建中所設定的條件。

    ![現有組建管線的範例](media/batch-ci-cd/existing-build-pipeline.jpg)

1. 流覽至 Azure Pipelines 的 [**組建**] 區段，以在 Azure DevOps 中觀看組建進度的即時更新。 從您的組建定義中選取適當的組建。

    ![從組建中查看即時輸出](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> 如果您使用用戶端應用程式來執行 HPC Batch 應用程式，您需要為該應用程式建立個別的組建定義。 您可以在[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops)檔中找到許多使用方法指南。

## <a name="continuous-deployment"></a>連續部署

Azure Pipelines 也用來部署您的應用程式和基礎結構。 [發行管線](https://docs.microsoft.com/azure/devops/pipelines/release)是一種元件，可啟用持續部署並自動化您的發行流程。

### <a name="deploying-your-application-and-underlying-infrastructure"></a>部署您的應用程式和基礎結構

部署基礎結構牽涉到幾個步驟。 因為我們已使用[連結的範本](../azure-resource-manager/templates/linked-templates.md)，所以必須可從公用端點（HTTP 或 HTTPS）存取這些範本。 這可能是 GitHub 上的儲存機制，或 Azure Blob 儲存體帳戶或其他儲存位置。 已上傳的範本成品可以維持安全，因為它們可以保存在私用模式中，但使用某種形式的共用存取簽章（SAS）權杖來存取。 下列範例示範如何使用 Azure 儲存體 blob 的範本來部署基礎結構。

1. 建立**新的發行定義**，然後選取空白的定義。 接著，我們需要將新建立的環境重新命名為與管線相關的專案。

    ![初始發行管線](media/batch-ci-cd/Release-0.jpg)

1. 建立對組建管線的相依性，以取得 HPC 應用程式的輸出。

    > [!NOTE]
    > 同樣地，請記下**來源別名**，因為在發行定義內建立工作時，將會需要這項作業。

    ![在適當的組建管線中建立 HPCApplicationPackage 的成品連結](media/batch-ci-cd/Release-1.jpg)

1. 建立另一個成品的連結，這次是 Azure 存放庫。 若要存取儲存在存放庫中的 Resource Manager 範本，這是必要的。 因為 Resource Manager 範本不需要編譯，所以您不需要透過組建管線推送它們。

    > [!NOTE]
    > 同樣地，請記下**來源別名**，因為在發行定義內建立工作時，將會需要這項作業。

    ![建立 Azure Repos 的成品連結](media/batch-ci-cd/Release-2.jpg)

1. 流覽至 [**變數**] 區段。 建議您在管線中建立一些變數，因此不會在多個工作中輸入相同的資訊。 這些是此範例中使用的變數，以及它們如何影響部署。

    * **applicationStorageAccountName**：用來保存 HPC 應用程式二進位檔的儲存體帳戶名稱
    * **batchAccountApplicationName**： Azure Batch 帳戶中的應用程式名稱
    * **batchAccountName**： Azure Batch 帳戶的名稱
    * **batchAccountPoolName**：執行處理的 vm 集區名稱
    * **batchApplicationId**： Azure Batch 應用程式的唯一識別碼
    * **batchApplicationVersion**： batch 應用程式的語義版本（也就是 ffmpeg 二進位檔）
    * **位置**：要部署的 Azure 資源位置
    * **resourceGroupName**：要建立之資源群組的名稱，以及您的資源將部署在何處
    * **storageAccountName**：用來保存連結 Resource Manager 範本之儲存體帳戶的名稱

    ![Azure Pipelines 版本設定的變數範例](media/batch-ci-cd/Release-4.jpg)

1. 流覽至開發環境的工作。 在下列快照中，您可以看到六個工作。 這些工作將會：下載壓縮的 ffmpeg 檔案、部署儲存體帳戶來裝載嵌套 Resource Manager 範本、將這些 Resource Manager 範本複製到儲存體帳戶、部署 batch 帳戶和必要的相依性、在 Azure Batch 帳戶中建立應用程式，以及將應用程式封裝上傳到 Azure Batch 帳戶。

    ![用來釋放 HPC 應用程式以 Azure Batch 的工作範例](media/batch-ci-cd/Release-3.jpg)

1. 新增 [**下載管線成品（預覽）** ] 工作，並設定下列屬性：
    * **顯示名稱：** 將 ApplicationPackage 下載至代理程式
    * **要下載的成品名稱：** hpc-應用程式
    * 要**下載的路徑**： $ （system.defaultworkingdirectory）

1. 建立儲存體帳戶來儲存您的構件。 您可以使用解決方案中現有的儲存體帳戶，但對於獨立範例和內容的隔離，我們會為成品建立專用的儲存體帳戶（特別是 Resource Manager 範本）。

    新增 [ **Azure 資源群組部署**] 工作，並設定下列屬性：
    * **顯示名稱：** 部署 Resource Manager 範本的儲存體帳戶
    * **Azure 訂**用帳戶：選取適當的 Azure 訂用帳戶
    * **動作**：建立或更新資源群組
    * **資源群組**： $ （resourceGroupName）
    * **位置**： $ （位置）
    * **範本**： $ （ArtifactsDirectory）/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.json
    * 覆**寫範本參數**：-accountName $ （storageAccountName）

1. 從原始檔控制將成品上傳至儲存體帳戶。 有一個 Azure 管線工作可執行此作業。 在這項工作中，可以將儲存體帳戶容器 URL 和 SAS 權杖輸出到 Azure Pipelines 中的變數。 這表示可以在此代理程式階段重複使用它。

    新增**Azure 檔案複製**工作並設定下列屬性：
    * **來源：** $ （ArtifactsDirectory）/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/
    * **Azure 連線類型**： Azure Resource Manager
    * **Azure 訂**用帳戶：選取適當的 Azure 訂用帳戶
    * **目的地類型**： Azure Blob
    * **RM 儲存體帳戶**： $ （storageAccountName）
    * **容器名稱**：範本
    * **儲存體容器 URI**： templateContainerUri
    * **儲存體容器 SAS 權杖**： templateContainerSasToken

1. 部署 orchestrator 範本。 回想一下先前的 orchestrator 範本，您會發現除了 SAS 權杖以外，儲存體帳戶容器 URL 有參數。 您應該會注意到，Resource Manager 範本中所需的變數會保留在發行定義的 variables 區段中，或是從另一個 Azure Pipelines 工作所設定（例如，Azure Blob 複製工作的一部分）。

    新增 [ **Azure 資源群組部署**] 工作，並設定下列屬性：
    * **顯示名稱：** 部署 Azure Batch
    * **Azure 訂**用帳戶：選取適當的 Azure 訂用帳戶
    * **動作**：建立或更新資源群組
    * **資源群組**： $ （resourceGroupName）
    * **位置**： $ （位置）
    * **範本**： $ （ArtifactsDirectory）/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.json
    * 覆**寫範本參數**：```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

常見的做法是使用 Azure Key Vault 的工作。 如果服務主體（與您的 Azure 訂用帳戶的連線）已設定適當的存取原則，則它可以從 Azure Key Vault 下載秘密，並作為管線中的變數使用。 密碼的名稱會設定為相關聯的值。 例如，可以在發行定義中使用 $ （sshPassword）來參考 sshPassword 的秘密。

1. 接下來的步驟會呼叫 Azure CLI。 第一個是用來在 Azure Batch 中建立應用程式。 並上傳相關聯的套件。

    新增**Azure CLI**工作並設定下列屬性：
    * **顯示名稱：** 在 Azure Batch 帳戶中建立應用程式
    * **Azure 訂**用帳戶：選取適當的 Azure 訂用帳戶
    * **腳本位置**：內嵌腳本
    * **內嵌腳本**：```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. 第二個步驟是用來將相關聯的封裝上傳至應用程式。 在我們的案例中是 ffmpeg 檔案。

    新增**Azure CLI**工作並設定下列屬性：
    * **顯示名稱：** 將套件上傳至 Azure Batch 帳戶
    * **Azure 訂**用帳戶：選取適當的 Azure 訂用帳戶
    * **腳本位置**：內嵌腳本
    * **內嵌腳本**：```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > 應用程式封裝的版本號碼會設定為變數。 如果覆寫先前版本的封裝適用于您，而且您想要手動控制推送至 Azure Batch 之封裝的版本號碼，這會很方便。

1. 選取 [**發行] > 建立新的**版本，以建立新的發行。 一旦觸發，請選取新版本的連結以查看狀態。

1. 您可以藉由選取環境下方的 [**記錄**] 按鈕，來查看代理程式的即時輸出。

    ![查看發行的狀態](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>測試環境

設定環境之後，請確認下列測試可以順利完成。

從 PowerShell 命令提示字元使用 Azure CLI，連接到新的 Azure Batch 帳戶。

* 使用`az login`登入您的 Azure 帳戶，並遵循指示進行驗證。
* 現在驗證 Batch 帳戶：`az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>列出可用的應用程式

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>檢查集區是否有效

```azurecli
az batch pool list
```

請記下此`currentDedicatedNodes`命令輸出中的值。 這個值會在下一個測試中調整。

#### <a name="resize-the-pool"></a>調整集區大小

調整集區大小，讓計算節點可供作業和工作測試使用，請使用 [集區清單] 命令查看目前的狀態，直到調整大小完成且有可用的節點為止

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>後續步驟

除了本文外，還有兩個教學課程會使用 .NET 和 Python 來利用 ffmpeg。 如需有關如何透過簡單的應用程式與 Batch 帳戶進行互動的詳細資訊，請參閱這些教學課程。

* [使用 Python API 透過 Azure Batch 執行平行工作負載](tutorial-parallel-python.md)
* [使用 .NET API 透過 Azure Batch 執行平行工作負載](tutorial-parallel-dotnet.md)
