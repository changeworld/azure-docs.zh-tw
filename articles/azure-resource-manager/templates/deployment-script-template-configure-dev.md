---
title: 在範本中設定部署腳本的開發環境 |Microsoft Docs
description: 在 Azure Resource Manager 範本中設定部署腳本的開發環境。
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: jgao
ms.openlocfilehash: 232a1ae5d125a2ea1d5723e85073fb3dd02420cc
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294072"
---
# <a name="configure-development-environment-for-deployment-scripts-in-templates-preview"></a>在範本中設定部署腳本的開發環境（預覽）

瞭解如何建立開發環境，以使用部署腳本映射來開發和測試部署腳本。 您可以建立[Azure 容器實例](../../container-instances/container-instances-overview.md)或使用[Docker](https://docs.docker.com/get-docker/)。 這兩者都涵蓋在本文中。

## <a name="prerequisites"></a>先決條件

如果您沒有部署腳本，可以建立包含下列內容的**hello.ps1**檔案：

```powershell
param([string] $name)
$output = 'Hello {0}' -f $name
Write-Output $output
$DeploymentScriptOutputs = @{}
$DeploymentScriptOutputs['text'] = $output
```

## <a name="use-azure-container-instance"></a>使用 Azure 容器實例

若要在電腦上撰寫腳本，您必須建立儲存體帳戶，並將儲存體帳戶掛接到容器實例。 如此一來，您就可以將腳本上傳至儲存體帳戶，並在容器實例上執行腳本。

> [!NOTE]
> 您建立用來測試腳本的儲存體帳戶不是部署腳本服務用來執行腳本的相同儲存體帳戶。 部署腳本服務會在每次執行時，建立一個唯一的名稱做為檔案共用。

### <a name="create-an-azure-container-instance"></a>建立 Azure 容器實例

下列 ARM 範本會建立容器實例和檔案共用，然後將檔案共用裝載至容器映射。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
      "defaultValue": "mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "deploymentScript",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('projectName'), 'store')]",
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
掛接路徑的預設值為**deploymentScript**。  這是在容器實例中掛接至檔案共用的路徑。

範本中指定的預設容器映射為**mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3 "**。  如需支援的 Azure PowerShell 版本和 Azure CLI 版本清單，請參閱[Azure PowerShell 或 Azure CLI](./deployment-script-template.md#prerequisites)。

範本會暫停容器實例1800秒。 您有30分鐘的時間，容器實例進入終端機狀態，而且會話結束。

若要部署範本：

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-azResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-deployment-script"></a>上傳部署腳本

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

您也可以使用 Azure 入口網站和 Azure CLI 上傳檔案。

### <a name="test-the-deployment-script"></a>測試部署腳本

1. 從 [Azure 入口網站] 中，開啟您已部署容器實例和儲存體帳戶的資源群組。
1. 開啟容器群組。 預設容器組名是已附加**cg**的專案名稱。 您應該會看到容器實例**處於 [執行**中] 狀態。
1. 從左側功能表中選取 [**容器**]。 您應該會看到一個容器實例。  容器實例名稱是已附加**容器**的專案名稱。

    ![部署腳本連接容器實例](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

1. 選取 **[連線]**，然後選取 **[連線]**。 如果您無法連接到容器實例，請重新開機容器群組，然後再試一次。
1. 在主控台窗格中，執行下列命令：

    ```
    cd deploymentScript
    ls
    pwsh ./hello.ps1 "John Dole"
    ```

    輸出是**Hello John Dole**。

    ![部署腳本容器實例測試](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test.png)

## <a name="use-docker"></a>使用 Docker

您可以使用預先設定的 Docker 容器映像做為部署指令碼開發環境。 若要安裝 Docker，請參閱[取得 docker](https://docs.docker.com/get-docker/)。
您也需要設定檔案共用，將包含部署腳本的目錄掛接到 Docker 容器。

1. 將部署指令碼容器映像提取到本機電腦：

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    此範例使用 PowerShell 版本4.3.0。

    從 Microsoft Container Registry (MCR) 提取 CLI 映像：

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    此範例使用 CLI 2.0.80 版。 部署指令碼會使用[這裡](https://hub.docker.com/_/microsoft-azure-cli)找到的預設 CLI 容器映像。

1. 在本機執行 Docker 映像。

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    以共用磁碟機上的現有資料夾取代 **&lt;主機驅動程式代號>** 和 **&lt;主機目錄名稱>** 。  其會將資料夾對應至容器中的 **/data** 資料夾。 例如，對應 D:\docker：

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **-it** 表示讓容器映像保持運作。

    CLI 範例：

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. 下列螢幕擷取畫面顯示如何執行 PowerShell 腳本（假設您在共用磁片磁碟機中有 helloworld.ps1 檔案）。

    ![Resource Manager 範本部署指令碼 Docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

成功測試腳本之後，您可以使用它做為範本中的部署腳本。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何使用部署指令碼。 逐步解說部署指令碼教學課程：

> [!div class="nextstepaction"]
> [教學課程：使用 Azure Resource Manager 範本中的部署指令碼](./template-tutorial-deployment-script.md)
