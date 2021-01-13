---
title: 針對範本中的部署腳本設定開發環境 |Microsoft Docs
description: 在 Azure Resource Manager 範本中設定部署腳本的開發環境 (ARM 範本) 。
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: jgao
ms.openlocfilehash: f731236b235883f019c74ef0b32f5066ca5b7514
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179362"
---
# <a name="configure-development-environment-for-deployment-scripts-in-arm-templates"></a>在 ARM 範本中設定部署腳本的開發環境

瞭解如何使用部署腳本映射來建立開發和測試 ARM 範本部署腳本的開發環境。 您可以建立 [Azure 容器實例](../../container-instances/container-instances-overview.md) ，或使用 [Docker](https://docs.docker.com/get-docker/)。 本文涵蓋這兩個選項。

## <a name="prerequisites"></a>先決條件

### <a name="azure-powershell-container"></a>Azure PowerShell 容器

如果您沒有 Azure PowerShell 部署腳本，您可以使用下列內容建立 *hello.ps1* 檔案：

```powershell
param([string] $name)
$output = 'Hello {0}' -f $name
Write-Output $output
$DeploymentScriptOutputs = @{}
$DeploymentScriptOutputs['text'] = $output
```

### <a name="azure-cli-container"></a>Azure CLI 容器

針對 Azure CLI 的容器映射，您可以使用下列內容建立 *hello.sh* 檔案：

```bash
firstname=$1
lastname=$2
output="{\"name\":{\"displayName\":\"$firstname $lastname\",\"firstName\":\"$firstname\",\"lastName\":\"$lastname\"}}"
echo -n "Hello "
echo $output | jq -r '.name.displayName'
```

> [!NOTE]
> 當您執行 Azure CLI 部署腳本時，名為的環境變數會 `AZ_SCRIPTS_OUTPUT_PATH` 儲存腳本輸出檔案的位置。 環境變數無法在開發環境容器中使用。 如需使用 Azure CLI 輸出的詳細資訊，請參閱使用 [CLI 腳本的輸出](deployment-script-template.md#work-with-outputs-from-cli-script)。

## <a name="use-azure-powershell-container-instance"></a>使用 Azure PowerShell 容器實例

若要在電腦上撰寫您的腳本，您必須建立儲存體帳戶，並將儲存體帳戶掛接到容器實例。 如此您就可以將腳本上傳至儲存體帳戶，並在容器實例上執行腳本。

> [!NOTE]
> 您所建立用來測試腳本的儲存體帳戶，與部署腳本服務用來執行腳本的儲存體帳戶不同。 部署腳本服務會在每次執行時，將唯一的名稱建立為檔案共用。

### <a name="create-an-azure-powershell-container-instance"></a>建立 Azure PowerShell 的容器實例

下列 Azure Resource Manager 範本 (ARM 範本) 建立容器實例和檔案共用，然後將檔案共用掛接至容器映射。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "type": "string",
      "metadata": {
        "description": "Specify a project name that is used for generating resource names."
      }
    },
    "containerImage": {
      "type": "string",
      "defaultValue": "mcr.microsoft.com/azuredeploymentscripts-powershell:az5.2",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "/mnt/azscripts/azscriptinput",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[tolower(concat(parameters('projectName'), 'store'))]",
    "fileShareName": "[concat(parameters('projectName'), 'share')]",
    "containerGroupName": "[concat(parameters('projectName'), 'cg')]",
    "containerName": "[concat(parameters('projectName'), 'container')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    },
    {
      "type": "Microsoft.Storage/storageAccounts/fileServices/shares",
      "apiVersion": "2019-06-01",
      "name": "[concat(variables('storageAccountName'), '/default/', variables('fileShareName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "name": "[variables('containerGroupName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ],
      "properties": {
        "containers": [
          {
            "name": "[variables('containerName')]",
            "properties": {
              "image": "[parameters('containerImage')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "protocol": "TCP",
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "[parameters('mountPath')]"
                }
              ],
              "command": [
                "/bin/sh",
                "-c",
                "pwsh -c 'Start-Sleep -Seconds 1800'"
              ]
            }
          }
        ],
        "osType": "Linux",
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
              "readOnly": false,
              "shareName": "[variables('fileShareName')]",
              "storageAccountName": "[variables('storageAccountName')]",
              "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
            }
          }
        ]
      }
    }
  ]
}
```

掛接路徑的預設值為 `/mnt/azscripts/azscriptinput` 。 這是容器實例中裝載至檔案共用的路徑。

範本中指定的預設容器映射是 **mcr.microsoft.com/azuredeploymentscripts-powershell:az5.2**。 查看所有 [支援之 Azure PowerShell 版本](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)的清單。

此範本會在1800秒後暫停容器實例。 您有30分鐘的時間，容器實例進入終止狀態，且會話結束。

若要部署範本：

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-the-deployment-script"></a>上傳部署腳本

將您的部署腳本上傳至儲存體帳戶。 以下是 PowerShell 腳本的範例：

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

您也可以使用 Azure 入口網站或 Azure CLI 來上傳檔案。

### <a name="test-the-deployment-script"></a>測試部署腳本

1. 在 Azure 入口網站中，開啟您在其中部署容器實例和儲存體帳戶的資源群組。
2. 開啟容器群組。 預設容器組名是附加了 *cg* 的專案名稱。 容器實例處於執行 **中狀態。**
3. 在 [資源] 功能表中，選取 [ **容器**]。 容器實例名稱是附加至 *容器* 的專案名稱。

    ![Azure 入口網站中的部署腳本 connect 容器實例的螢幕擷取畫面。](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

4. 選取 **[連接]**，然後選取 **[連接]**。 如果您無法連接到容器實例，請重新開機容器群組，然後再試一次。
5. 在主控台窗格中，執行下列命令：

    ```console
    cd /mnt/azscripts/azscriptinput
    ls
    pwsh ./hello.ps1 "John Dole"
    ```

    輸出為 **Hello John Dole**。

    ![主控台中的部署腳本 connect 容器實例測試輸出的螢幕擷取畫面。](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test.png)

## <a name="use-an-azure-cli-container-instance"></a>使用 Azure CLI 的容器實例

若要在電腦上撰寫您的腳本，請建立儲存體帳戶，並將儲存體帳戶掛接到容器實例。 然後，您可以將腳本上傳至儲存體帳戶，並在容器實例上執行腳本。

> [!NOTE]
> 您所建立用來測試腳本的儲存體帳戶，與部署腳本服務用來執行腳本的儲存體帳戶不同。 部署腳本服務會在每次執行時，將唯一的名稱建立為檔案共用。

### <a name="create-an-azure-cli-container-instance"></a>建立 Azure CLI 的容器實例

下列 ARM 範本會建立容器實例和檔案共用，然後將檔案共用掛接至容器映射：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "type": "string",
      "metadata": {
        "description": "Specify a project name that is used for generating resource names."
      }
    },
    "containerImage": {
      "type": "string",
      "defaultValue": "mcr.microsoft.com/azure-cli:2.9.1",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "/mnt/azscripts/azscriptinput",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[tolower(concat(parameters('projectName'), 'store'))]",
    "fileShareName": "[concat(parameters('projectName'), 'share')]",
    "containerGroupName": "[concat(parameters('projectName'), 'cg')]",
    "containerName": "[concat(parameters('projectName'), 'container')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    },
    {
      "type": "Microsoft.Storage/storageAccounts/fileServices/shares",
      "apiVersion": "2019-06-01",
      "name": "[concat(variables('storageAccountName'), '/default/', variables('fileShareName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "name": "[variables('containerGroupName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ],
      "properties": {
        "containers": [
          {
            "name": "[variables('containerName')]",
            "properties": {
              "image": "[parameters('containerImage')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "protocol": "TCP",
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "[parameters('mountPath')]"
                }
              ],
              "command": [
                "/bin/bash",
                "-c",
                "echo hello; sleep 1800"
              ]
            }
          }
        ],
        "osType": "Linux",
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
              "readOnly": false,
              "shareName": "[variables('fileShareName')]",
              "storageAccountName": "[variables('storageAccountName')]",
              "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
            }
          }
        ]
      }
    }
  ]
}
```

掛接路徑的預設值為 `/mnt/azscripts/azscriptinput` 。 這是容器實例中裝載至檔案共用的路徑。

範本中指定的預設容器映射是 **mcr.microsoft.com/azure-cli:2.9.1**。 請參閱支援的 [Azure CLI 版本](https://mcr.microsoft.com/v2/azure-cli/tags/list)清單。

> [!IMPORTANT]
> 部署腳本會使用來自 Microsoft Container Registry 的可用 CLI 映射 (MCR) 。 需要大約一個月的認證，以驗證部署腳本的 CLI 映射。 請勿使用在 30 天內發行的 CLI 版本。 若要尋找映像的發行日期，請參閱 [Azure CLI 版本資訊](/cli/azure/release-notes-azure-cli?view=azure-cli-latest&preserve-view=true)。 如果您使用不支援的版本，則錯誤訊息會列出支援的版本。

此範本會在1800秒後暫停容器實例。 您有30分鐘的時間，您的容器實例進入終端狀態，且會話結束。

若要部署範本：

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-the-deployment-script"></a>上傳部署腳本

將您的部署腳本上傳至儲存體帳戶。 以下是 PowerShell 範例：

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

您也可以使用 Azure 入口網站或 Azure CLI 來上傳檔案。

### <a name="test-the-deployment-script"></a>測試部署腳本

1. 在 Azure 入口網站中，開啟您在其中部署容器實例和儲存體帳戶的資源群組。
1. 開啟容器群組。 預設容器組名是附加了 *cg* 的專案名稱。 容器實例會顯示為 [執行中 **] 狀態。**
1. 在 [資源] 功能表中，選取 [ **容器**]。 容器實例名稱是附加至 *容器* 的專案名稱。

    ![部署腳本 connect 容器實例](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

1. 選取 **[連接]**，然後選取 **[連接]**。 如果您無法連接到容器實例，請重新開機容器群組，然後再試一次。
1. 在主控台窗格中，執行下列命令：

    ```console
    cd /mnt/azscripts/azscriptinput
    ls
    ./hello.sh John Dole
    ```

    輸出為 **Hello John Dole**。

    ![部署腳本容器實例測試](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test-cli.png)

## <a name="use-docker"></a>使用 Docker

您可以使用預先設定的 Docker 容器映射作為部署腳本開發環境。 若要安裝 Docker，請參閱 [取得 docker](https://docs.docker.com/get-docker/)。
您也需要設定檔案共用，以將包含部署腳本的目錄掛接至 Docker 容器。

1. 將部署指令碼容器映像提取到本機電腦：

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    此範例會使用 PowerShell 4.3.0 版。

    從 MCR 提取 CLI 映射：

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    此範例使用 CLI 2.0.80 版。 部署指令碼會使用[這裡](https://hub.docker.com/_/microsoft-azure-cli)找到的預設 CLI 容器映像。

1. 在本機執行 Docker 映射。

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    以共用磁碟機上的現有資料夾取代 **&lt;主機驅動程式代號>** 和 **&lt;主機目錄名稱>** 。 其會將資料夾對應至容器中的 _/data_ 資料夾。 例如，若要對應 _D:\docker_：

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **-it** 表示讓容器映像保持運作。

    CLI 範例：

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. 下列螢幕擷取畫面顯示當您在共用磁片磁碟機中有 *helloworld.ps1* 檔案時，如何執行 PowerShell 腳本。

    ![Resource Manager 範本部署指令碼 Docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

成功測試腳本之後，您可以使用它做為範本中的部署腳本。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何使用部署指令碼。 逐步解說部署指令碼教學課程：

> [!div class="nextstepaction"]
> [教學課程：在 ARM 範本中使用部署腳本](./template-tutorial-deployment-script.md)
