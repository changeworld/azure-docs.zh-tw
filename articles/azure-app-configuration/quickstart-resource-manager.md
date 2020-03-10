---
title: 使用 Azure 應用程式組態進行自動化 VM 部署快速入門
description: 本快速入門示範如何使用 Azure PowerShell 模組和 Azure Resource Manager 範本來部署 Azure 應用程式組態存放區。 然後使用存放區中的值來部署 VM。
author: lisaguthrie
ms.author: lcozzens
ms.date: 03/05/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom: mvc
ms.openlocfilehash: 7c6f4b2ea9494c004067a8b19df2c2f098ac2b7f
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274507"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration"></a>快速入門：使用應用程式組態進行自動化 VM 部署

使用 Azure PowerShell 模組，即可利用 PowerShell Cmdlet 或指令碼建立和管理 Azure 資源。 本快速入門說明如何使用 Azure PowerShell 和 Azure Resource Manager 範本來部署 Azure 應用程式組態存放區。 然後，您會了解如何使用存放區中的索引鍵/值來部署 VM。

您可以使用必要條件範本來建立應用程式組態存放區，然後使用 Azure 入口網站或 Azure CLI，將索引鍵/值新增至存放區。 主要範本會參考現有組態存放區中的現有索引鍵/值組態。 所擷取的值可用來設定範本所建立資源的屬性，例如此範例中的 VM。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/)。

* 本快速入門需要 Azure PowerShell 模組。 執行 `Get-Module -ListAvailable Az` 來尋找本機電腦上所安裝的版本。 如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-Az-ps)。

## <a name="sign-in-to-azure"></a>登入 Azure

使用 `Connect-AzAccount` 命令登入 Azure 訂用帳戶，並在快顯瀏覽器中輸入您的 Azure 認證：

```azurepowershell-interactive
# Connect to your Azure account
Connect-AzAccount
```

如果您有多個訂用帳戶，請執行下列 Cmdlet 以選取您想要用於本快速入門的訂用帳戶。 別忘了以您的訂用帳戶名稱取代 `<your subscription name>`：

```azurepowershell-interactive
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 來建立 Azure 資源群組。 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

```azurepowershell-interactive
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="deploy-an-azure-app-configuration-store"></a>部署 Azure 應用程式組態存放區

將索引鍵/值套用至 VM 之前，您必須具備現有的 Azure 應用程式組態存放區。 本節詳細說明如何使用 Azure Resource Manager 範本來部署 Azure 應用程式組態存放區。 如果您已經有應用程式組態存放區，則可移至本文的下一節。 

1. 複製下列 json 程式碼並貼到名為 *prereq.azuredeploy.json* 的新檔案中。

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "type": "string",
        "metadata": {
          "description": "Specifies the name of the app configuration store."
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Specifies the Azure location where the app configuration store should be created."
        }
      },
      "skuName": {
        "type": "string",
        "defaultValue": "free",
        "metadata": {
          "description": "Specifies the SKU of the app configuration store."
        }
      }
    },
    "resources": [
      {
        "type": "Microsoft.AppConfiguration/configurationStores",
        "name": "[parameters('configStoreName')]",
        "apiVersion": "2019-10-01",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('skuName')]"
        }
      }
    ]
   }
   ```

1. 複製下列 json 程式碼並貼到名為 *prereq.azuredeploy.parameters.json*的新檔案中。 以您的組態存放區的唯一名稱取代 **GET-UNIQUE**。

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "value": "GET-UNIQUE"
      }
    }
   }
   ```

1. 在您的 PowerShell 視窗中，執行下列命令以部署 Azure 應用程式組態存放區。 別忘了取代資源群組名稱、範本檔案路徑和範本參數檔案路徑。

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

## <a name="add-vm-configuration-key-values"></a>新增 VM 組態索引鍵/值

您可以使用 Azure Resource Manager 範本來建立應用程式組態存放區，但是需要使用 Azure 入口網站或 Azure CLI 來新增索引鍵/值。 在本快速入門中，您會使用 Azure 入口網站新增索引鍵/值。

1. 部署完成之後，請在 [Azure 入口網站](https://portal.azure.com)中瀏覽至新建立的應用程式組態存放區。

1. 選取 [設定]   > [存取金鑰]  。 請記下連接字串的主要唯讀索引鍵。 稍後，您會使用此連接字串來設定您的應用程式，使其與您建立的應用程式組態存放區進行通訊。

1. 選取 [組態總管]   > [建立]  以新增下列索引鍵/值組：

   |Key|值|
   |-|-|
   |windowsOsVersion|2019-Datacenter|
   |diskSizeGB|1023|
  
   針對 [標籤]  輸入 *template*，但是將 [內容類型]  保留空白。

## <a name="deploy-vm-using-stored-key-values"></a>使用儲存的索引鍵/值部署 VM

您現已將索引鍵/值新增至存放區，就可以使用 Azure Resource Manager 範本來部署 VM。 此範本會參考您所建立的 **windowsOsVersion** 和 **diskSizeGB** 金鑰。

1. 複製下列 json 程式碼並貼到名為 *azuredeploy.json* 的新檔案中，或從 [Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.json)下載檔案。

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "Admin user name."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Password for the Virtual Machine."
            }
        },
        "appConfigStoreName": {
            "type": "string",
            "metadata": {
                "description": "App configuration store name."
            }
        },
        "appConfigStoreResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Name of the resource group for the app config store."
            }
        },
        "domainNameLabel": {
            "type": "string",
            "metadata": {
                "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_D2_v3",
            "metadata": {
                "description": "Size of the VM"
            }
        },
        "vmSkuKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM windows sku"
            }
        },
        "diskSizeKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM disk size"
            }
        },
        "storageAccountName": {
            "type": "string",
            "metadata": {
                "description": "The name of the storage account."
            }
        }
    },
    "variables": {
        "nicName": "myVMNic",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "publicIPAddressName": "myPublicIP",
        "vmName": "SimpleWinVM",
        "virtualNetworkName": "MyVNET",
        "subnetRef": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworkName'), variables('subnetName'))]",
        "appConfigRef": "[resourceId(parameters('appConfigStoreResourceGroup'), 'Microsoft.AppConfiguration/configurationStores', parameters('appConfigStoreName'))]",
        "windowsOSVersionParameters": {
            "key": "[parameters('vmSkuKey')]",
            "label": "template"
        },
        "diskSizeGBParameters": {
            "key": "[parameters('diskSizeKey')]",
            "label": "template"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-11-01",
            "name": "[parameters('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[variables('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('domainNameLabel')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2018-11-01",
            "name": "[variables('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnetName')]",
                        "properties": {
                            "addressPrefix": "[variables('subnetPrefix')]"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2018-11-01",
            "name": "[variables('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[resourceId('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2018-10-01",
            "name": "[variables('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computerName": "[variables('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "[listKeyValue(variables('appConfigRef'), '2019-02-01-preview', variables('windowsOSVersionParameters')).value]",
                        "version": "latest"
                    },
                    "osDisk": {
                        "createOption": "FromImage"
                    },
                    "dataDisks": [
                        {
                            "diskSizeGB": "[listKeyValue(variables('appConfigRef'), '2019-02-01-preview', variables('diskSizeGBParameters')).value]",
                            "lun": 0,
                            "createOption": "Empty"
                        }
                    ]
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                        }
                    ]
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))).primaryEndpoints.blob]"
                    }
                }
            }
        }
    ],
    "outputs": {
        "hostname": {
            "type": "string",
            "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]"
        }
    }
   }
   ```

1. 複製下列 json 程式碼並貼到名為 *azuredeploy.parameters.json* 的新檔案中，或從 [Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.parameters.json)下載檔案。

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "adminPassword": {
        "value": "GEN-PASSWORD"
      },
      "appConfigStoreName":{
        "value": "GEN-APPCONFIGSTORE-NAME"
      },
      "appConfigStoreResourceGroup": {
         "value": "GEN-APPCONFIGSTORE-RESOURCEGROUP-NAME"
      },
      "vmSkuKey":{
        "value": "GEN-APPCONFIGSTORE-WINDOWSOSVERSION"
      },
      "diskSizeKey" :{
         "value": "GEN-APPCONFIGSTORE-DISKSIZEGB"
      },
      "adminUsername":{
        "value": "GEN-UNIQUE"
      },
      "storageAccountName":{
        "value": "GEN-UNIQUE"
      },
      "domainNameLabel":{
        "value": "GEN-UNIQUE"
      }
    }
   }
   ```

   以下列值取代範本中的參數值：

   |參數|值|
   |-|-|
   |adminPassword|VM 的系統管理員密碼。|
   |appConfigStoreName|Azure 應用程式組態存放區的名稱。|
   |appConfigStoreResourceGroup|含有您的應用程式組態存放區的資源群組。|
   |vmSkuKey|*windowsOSVersion*|
   |diskSizeKey|*diskSizeGB*|
   |adminUsername|VM 的系統管理員使用者名稱。|
   |storageAccountName|與 VM 相關聯的儲存體帳戶唯一名稱。|
   |domainNameLabel|唯一的網域名稱。|

1. 在您的 PowerShell 視窗中，執行下列命令以部署 Azure 應用程式組態存放區。 別忘了取代資源群組名稱、範本檔案路徑和範本參數檔案路徑。

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

恭喜！ 您已使用 Azure 應用程式組態中儲存的組態來部署 VM。

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、應用程式組態存放區、VM 和所有相關資源。 如果您打算在未來使用應用程式組態存放區或 VM，則可跳過刪除動作。 如果您不要繼續使用此作業，請執行下列 Cmdlet，以刪除本快速入門所建立的所有資源：

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用 Azure 應用程式組態中的 Azure Resource Manager 範本和索引鍵/值來部署 VM。

若要了解如何使用 Azure 應用程式組態來建立其他應用程式，請繼續閱讀以下文章：

> [!div class="nextstepaction"]
> [快速入門：使用 Azure 應用程式組態建立 ASP.NET Core 應用程式](quickstart-aspnet-core-app.md)
