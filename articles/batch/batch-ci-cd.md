---
title: 使用 Azure Pipelines 來建置和部署 HPC 解決方案
description: 了解如何針對在 Azure Batch 上執行的 HPC 應用程式部署建置/發行管線。
author: chrisreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: how-to
ms.openlocfilehash: 72f976071a5fc65c8e96f6b3cd5c0094785e287b
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726837"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>使用 Azure Pipelines 來建置和部署 HPC 解決方案

Azure DevOps Services 會提供開發小組在建置自訂應用程式時所使用的各種工具。 Azure DevOps 所提供的工具可轉譯為高效能計算解決方案的自動化建置和測試。 本文示範如何使用 Azure Pipelines，針對在 Azure Batch 上部署的高效能計算解決方案，設定持續整合 (CI) 和持續部署 (CD)。

Azure Pipelines 提供各種新式 CI/CD 程序來建置、部署、測試及監視軟體。 這些程序可加速軟體交付，讓您專注於處理您的程式碼，而不是支援基礎結構和作業。

## <a name="create-an-azure-pipeline"></a>建立 Azure 管線

在此範例中，我們將建立建置和發行管線，以部署 Azure Batch 基礎結構及發行應用程式套件。 假設程式碼是在本機開發，這就是一般部署流程：

![此圖顯示管線中的部署流程](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>安裝程式

若要遵循本文中的步驟，您需要 Azure DevOps 組織和小組專案。

* [建立 Azure DevOps 組織](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [在 Azure DevOps 中建立專案](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>您環境的原始檔控制

原始檔控制可讓小組追蹤對程式碼庫所做的變更，並檢查先前的程式碼版本。

一般來說，原始檔控制會被視為軟體程式碼的密不可分。 基礎結構如何？ 這會將我們帶入「基礎結構即程式碼」，我們將在其中使用 Azure Resource Manager 範本或其他開放原始碼替代項目，以宣告方式定義基礎結構。

此範例高度依賴許多 Resource Manager 範本 (JSON 文件) 和現有的二進位檔。 您可以將這些範例複製到您的存放庫，並將其推送至 Azure DevOps。

此範例中使用的程式碼庫結構與下列項目類似：

* **arm-templates** 資料夾，其中包含數個 Azure Resource Manager 範本。 本文會說明這些範本。
* **client-application** 資料夾，這是[使用 ffmpeg 進行 Azure Batch .NET 檔案處理](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial)範例的複本。 這不是本文所需的項目。
* **hpc-application** 資料夾，這是 [ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip) 的 Windows 64 位元版本。
* **pipelines** 資料夾。 其中包含一個概述建置程序的 YAML 檔案。 這將在文件中討論。

本節假設您已熟悉版本控制和設計 Resource Manager 範本。 如果您不熟悉這些概念，請參閱下列頁面以取得詳細資訊。

* [什麼是原始檔控制？](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [了解 Azure Resource Manager 範本的結構和語法](../azure-resource-manager/templates/template-syntax.md)

#### <a name="azure-resource-manager-templates"></a>Azure 資源管理員範本

此範例利用多個 Resource Manager 範本來部署解決方案。 為了這麼做，我們會使用一些功能範本 (類似於單位或模組) 來執行特定功能。 我們也會使用端對端解決方案範本，其負責將這些基礎功能結合在一起。 這種方法有幾個優點：

* 基礎功能範本可以個別進行單元測試。
* 基礎功能範本可定義為組織內部的標準，並可在多個解決方案中重複使用。

在此範例中，有一個可部署三個範本的端對端解決方案範本 (deployment.json)。 基礎範本是功能範本，負責部署解決方案的特定層面。

![使用 Azure Resource Manager 範本的連結範本結構範例](media/batch-ci-cd/ARMTemplateHierarchy.png)

我們將查看的第一個範本是 Azure 儲存體帳戶。 我們的解決方案需要儲存體帳戶，才能將應用程式部署至我們的 Batch 帳戶。 在建立儲存體帳戶的 Resource Manager 範本時，值得留意 [Microsoft.Storage 資源類型的 Resource Manager 範本參考指南](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions)。

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

接下來，我們將探討 Azure Batch 帳戶範本。 Azure Batch 帳戶可作為跨集區 (機器群組) 執行許多應用程式的平台。 在建立 Batch 帳戶的 Resource Manager 範本時，值得留意 [Microsoft.Batch 資源類型的 Resource Manager 範本參考指南](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions)。

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

下一個範本顯示建立 Azure Batch 集區 (用來處理應用程式的後端機器) 的範例。 在建立 Batch 帳戶集區的 Resource Manager 範本時，值得留意 [Microsoft.Batch 資源類型的 Resource Manager 範本參考指南](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions)。

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

最後，我們有一個功用類似協調器的範本。 此範本負責部署功能範本。

您也可以在個別的文章中深入了解如何[建立連結的 Azure Resource Manager 範本](../azure-resource-manager/templates/template-tutorial-create-linked-templates.md)。

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

基礎結構和軟體均可定義為程式碼，並共置於相同的存放庫中。

在此解決方案中，ffmpeg 作為應用程式套件使用。 您可以在[這裡](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)下載 ffmpeg 套件。

![範例 Git 存放庫結構](media/batch-ci-cd/git-repository.jpg)

此存放庫有四個主要部分：

* **arm-templates** 資料夾，可儲存我們的「基礎結構即程式碼」
* **hpc-application** 資料夾，其中包含 ffmpeg 的二進位檔
* **pipelines** 資料夾，其中包含建置管線的定義。
* **選擇性**：**client-application** 資料夾，會儲存 .NET 應用程式的程式碼。 我們不會在範例中使用此資料夾，但是在您自己的專案中，您可能想要透過用戶端應用程式執行 HPC Batch 應用程式。

> [!NOTE]
> 這只是程式碼庫結構的其中一個範例。 這個方法用來示範應用程式、基礎結構和管線程式碼會儲存在相同的存放庫中。

現在已設定原始程式碼，我們可以開始第一個組建。

## <a name="continuous-integration"></a>持續整合

[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops) 位於 Azure DevOps Services 內，可協助您為應用程式實作建置、測試和部署管線。

在您管線的這個階段中，通常會執行測試來驗證程式代碼，並建置適當的軟體片段。 測試的數目和類型，以及您所執行的任何其他工作，都將取決於您更廣泛的建置和發行策略。

## <a name="preparing-the-hpc-application"></a>準備 HPC 應用程式

在此範例中，我們會將焦點放在 **hpc-application** 資料夾。 **hpc-application** 資料夾是將從 Azure Batch 帳戶執行的 ffmpeg 軟體。

1. 瀏覽至 Azure DevOps 組織中 Azure Pipelines 的 [組建] 區段。 建立 [新管線]。

    ![建立新的建置管線](media/batch-ci-cd/new-build-pipeline.jpg)

1. 您有兩個選項可用來建立建置管線：

    a. [使用視覺化設計工具](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav)。 若要使用此工具，請按一下 [新管線] 頁面上的 [使用視覺化設計工具]。

    b. [使用 YAML 組建](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops)。 您可以按一下 [新管線] 頁面上的 Azure Repos 或 GitHub 選項，建立新的 YAML 管線。 或者，可以將下列範例儲存在原始檔控制中，按一下 [視覺化設計工具]，然後使用 YAML 範本來參考現有的 YAML 檔案。

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

1. 視需要設定組建後，請選取 [儲存並排入佇列]。 如果您已啟用持續整合 (在 [觸發程序] 區段中)，則會在對存放庫進行新認可，並符合組建中設定的條件時自動觸發組建。

    ![現有建置管線的範例](media/batch-ci-cd/existing-build-pipeline.jpg)

1. 瀏覽至 Azure Pipelines 的 [建置] 區段，以在 Azure DevOps 中檢視建置進度的即時更新。 從您的組建定義中選取適當的組建。

    ![檢視您組建的即時輸出](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> 如果您使用用戶端應用程式來執行 HPC Batch 應用程式，您需要為該應用程式建立個別的組建定義。 您可以在 [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) 文件中找到許多操作指南。

## <a name="continuous-deployment"></a>連續部署

Azure Pipelines 也用來部署您的應用程式和基礎結構。 [發行管線](https://docs.microsoft.com/azure/devops/pipelines/release)是一個可啟用持續部署以及將發行程序自動化的元件。

### <a name="deploying-your-application-and-underlying-infrastructure"></a>部署您的應用程式和基礎結構

部署基礎結構牽涉到幾個步驟。 因為我們已使用 [連結的範本](../azure-resource-manager/templates/linked-templates.md)，所以必須可從公用端點 (HTTP 或 HTTPS) 存取這些範本。 這可能是 GitHub 上的存放庫，或 Azure Blob 儲存體帳戶或其他儲存位置。 已上傳的範本成品可以保持安全，因為其可以私人模式保存，但使用某種形式的共用存取簽章 (SAS) 權杖來存取。 下列範例示範如何使用 Azure 儲存體 Blob 中的範本來部署基礎結構。

1. 建立 [新發行定義]，然後選取空白定義。 接著，我們需要將新建立的環境重新命名為與管線相關的名稱。

    ![初始發行管線](media/batch-ci-cd/Release-0.jpg)

1. 建立對建置管線的相依性，以取得 HPC 應用程式的輸出。

    > [!NOTE]
    > 同樣地，請注意 [來源別名]，因為在發行定義內建立工作時會需要此別名。

    ![在適當的建置管線中建立 HPCApplicationPackage 的成品連結](media/batch-ci-cd/Release-1.jpg)

1. 建立另一個成品的連結，這次是 Azure 存放庫。 需有此連結，才能存取儲存在存放庫中的 Resource Manager 範本。 因為 Resource Manager 範本不需要編譯，所以您不需要透過建置管線推送這些範本。

    > [!NOTE]
    > 同樣地，請注意 [來源別名]，因為在發行定義內建立工作時會需要此別名。

    ![建立 Azure Repos 的成品連結](media/batch-ci-cd/Release-2.jpg)

1. 瀏覽至 [變數] 區段。 建議您在管線中建立一些變數，才不會在多重工作中輸入相同的資訊。 以下是此範例中使用的變數，以及其對部署有何影響。

    * **applicationStorageAccountName**：保存 HPC 應用程式二進位檔的儲存體帳戶名稱
    * **batchAccountApplicationName**：Azure Batch 帳戶中的應用程式名稱
    * **batchAccountName**：Azure Batch 帳戶的名稱
    * **batchAccountPoolName**：執行處理的 VM 集區名稱
    * **batchApplicationId**：Azure Batch 應用程式的唯一識別碼
    * **batchApplicationVersion**：Batch 應用程式的語意版本 (也就是 ffmpeg 二進位檔)
    * **location**：要部署的 Azure 資源位置
    * **resourceGroupName**：要建立的資源群組名稱，以及將部署資源的位置
    * **storageAccountName**：保存連結 Resource Manager 範本的儲存體帳戶名稱

    ![針對 Azure Pipelines 發行設定的變數範例](media/batch-ci-cd/Release-4.jpg)

1. 瀏覽至開發環境的工作。 在下列快照集中，您可以看到六個工作。 這些工作將會：下載壓縮的 ffmpeg 檔案、部署儲存體帳戶來裝載巢狀 Resource Manager 範本、將這些 Resource Manager 範本複製到儲存體帳戶、部署 Batch 帳戶和必要的相依性、在 Azure Batch 帳戶中建立應用程式，以及將應用程式套件上傳到 Azure Batch 帳戶。

    ![用於將 HPC 應用程式發行至 Azure Batch 的工作範例](media/batch-ci-cd/Release-3.jpg)

1. 新增 [下載管線成品 (預覽)] 工作並設定下列屬性：
    * **顯示名稱：** 將 ApplicationPackage 下載至代理程式
    * **要下載的成品名稱**：hpc-application
    * **要下載至的路徑**：$(System.DefaultWorkingDirectory)

1. 建立儲存體帳戶以儲存您的成品。 您可使用解決方案中現有的儲存體帳戶，但是為了提供獨立範例和內容隔離，我們會為成品 (特別是 Resource Manager 範本) 建立專用的儲存體帳戶。

    新增 [Azure 資源群組部署] 工作並設定下列屬性：
    * **顯示名稱：** 為 Resource Manager 範本部署儲存體帳戶
    * **Azure 訂用帳戶：** 選取適當的 Azure 訂用帳戶
    * **動作**：建立或更新資源群組
    * **資源群組**：$(resourceGroupName)
    * **位置**：$(location)
    * **範本**：$(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/storageAccount.json
    * **覆寫範本參數**：-accountName $(storageAccountName)

1. 將成品從原始檔控制上傳至儲存體帳戶。 有一個 Azure 管線工作可執行此工作。 在這項工作中，可以將儲存體帳戶容器 URL 和 SAS 權杖輸出到 Azure Pipelines 中的變數。 這表示可以在此代理程式階段予以重複使用。

    新增 [Azure 檔案複製] 工作並設定下列屬性：
    * **來源：** $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/
    * **Azure 連線類型**：Azure Resource Manager
    * **Azure 訂用帳戶：** 選取適當的 Azure 訂用帳戶
    * **目的地類型**：Azure Blob
    * **RM 儲存體帳戶**：$(storageAccountName)
    * **容器名稱**：templates
    * **儲存體容器 URI**：templateContainerUri
    * **儲存體容器 SAS 權杖**：templateContainerSasToken

1. 部署協調器範本。 回想一下先前的範本範本，您會發現除了 SAS 權杖以外，還有儲存體帳戶容器 URL 適用的參數。 您應會注意到，Resource Manager 範本中所需的變數會保留在發行定義的 variables 區段中，或是從另一個 Azure Pipelines 工作進行設定 (例如，Azure Blob 複製工作的一部分)。

    新增 [Azure 資源群組部署] 工作並設定下列屬性：
    * **顯示名稱：** 部署 Azure Batch
    * **Azure 訂用帳戶：** 選取適當的 Azure 訂用帳戶
    * **動作**：建立或更新資源群組
    * **資源群組**：$(resourceGroupName)
    * **位置**：$(location)
    * **範本**：$(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/deployment.json
    * **覆寫範本參數**：```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

常見的做法是使用 Azure Key Vault 工作。 如果服務主體 (連線到您的 Azure 訂用帳戶) 已設定適當的存取原則，即可從 Azure Key Vault 下載秘密並作為您管線中的變數。 秘密的名稱會以相關聯的值進行設定。 例如，在發行定義中可以使用 $(sshPassword) 來參考 sshPassword 的秘密。

1. 接下來的步驟會呼叫 Azure CLI。 第一個用於在 Azure Batch 中建立應用程式， 以及上傳相關聯的套件。

    新增 [Azure CLI] 工作並設定下列屬性：
    * **顯示名稱：** 在 Azure Batch 帳戶中建立應用程式
    * **Azure 訂用帳戶：** 選取適當的 Azure 訂用帳戶
    * **指令碼位置**：內嵌指令碼
    * **內嵌指令碼**：```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. 第二個步驟用來將相關聯的套件上傳至應用程式。 在我們的案例中是 ffmpeg 檔案。

    新增 [Azure CLI] 工作並設定下列屬性：
    * **顯示名稱：** 將套件上傳至 Azure Batch 帳戶
    * **Azure 訂用帳戶：** 選取適當的 Azure 訂用帳戶
    * **指令碼位置**：內嵌指令碼
    * **內嵌指令碼**：```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > 應用程式套件的版本號碼會設定為變數。 如果覆寫先前的版本套件對您有效，而且您想要手動控制推送至 Azure Batch 的套件版本號碼，這就會很方便。

1. 選取 [發行] > [建立新發行]，以建立新的發行。 一旦觸發，請選取新發行的連結以檢視狀態。

1. 您可藉由選取您環境之下的 [記錄] 按鈕，檢視代理程式的即時輸出。

    ![檢視發行狀態](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>測試環境

設定環境後，請確認下列測試可順利完成。

從 PowerShell 命令提示字元使用 Azure CLI，連線到新的 Azure Batch 帳戶。

* 使用 `az login` 登入您的 Azure 帳戶，並遵循指示進行驗證。
* 現在驗證 Batch 帳戶：`az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>列出可用的應用程式

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>檢查集區是否有效

```azurecli
az batch pool list
```

請記下此命令輸出中的 `currentDedicatedNodes` 值。 這個值會在下一個測試中進行調整。

#### <a name="resize-the-pool"></a>調整集區大小

調整集區大小，讓計算節點可用於作業和工作測試，請使用集區清單命令來查看目前的狀態，直到調整大小完成且有可用的節點為止

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>後續步驟

除了本文以外，還有兩個教學課程會透過 .NET 和 Python 來利用 ffmpeg。 如需有關如何透過簡單的應用程式來與 Batch 帳戶互動的詳細資訊，請參閱下列教學課程。

* [使用 Python API 透過 Azure Batch 執行平行工作負載](tutorial-parallel-python.md)
* [使用 .NET API 透過 Azure Batch 執行平行工作負載](tutorial-parallel-dotnet.md)
