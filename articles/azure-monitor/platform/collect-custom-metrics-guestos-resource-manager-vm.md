---
title: 使用範本在 Azure 監視器中收集 Windows VM 計量
description: 使用 Windows 虛擬機器的 Resource Manager 範本將客體作業系統計量傳送至 Azure 監視器計量存放區
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: e747ca89912c36538bfb9d02986629fe57c5adcb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77657362"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>使用 Windows 虛擬機器的 Resource Manager 範本將客體作業系統計量傳送至 Azure 監視器計量存放區

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

您可以使用 Azure 監視器[診斷擴充功能](diagnostics-extension-overview.md)，從當作虛擬機器、雲端服務或 Service Fabric 叢集一部分執行的客體作業系統 (客體 OS) 收集計量與記錄。 擴充功能可以將遙測資料傳送到[許多不同位置](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)。

此文章說明將 Windows 虛擬機器的客體 OS 效能計量傳送至 Azure 監視器資料存放區的程序。 從診斷 1.11 版開始，您可以直接將計量寫入到已收集標準平台計量的 Azure 監視器計量存放區。

將它們儲存在此位置可讓您存取與平台計量相同的動作。 動作包括近乎即時的警示、圖表、路由，以及從 REST API 存取和更多功能。 在過去，診斷擴充功能會寫入到 Azure 儲存體，而不是 Azure 監視器資料存放區。

如果您不熟悉 Resource Manager 範本，請瞭解[範本部署](../../azure-resource-manager/management/overview.md)及其結構和語法。

## <a name="prerequisites"></a>先決條件

- 您的訂用帳戶必須向[Microsoft Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services)註冊。

- 您需要安裝 [Azure PowerShell](/powershell/azure) 或 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)。

- 您的 VM 資源必須位於[支援自訂計量的區域](metrics-custom-overview.md#supported-regions)中。 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>設定 Azure 監視器作為資料接收器
Azure 診斷擴充功能會使用稱為「資料接收器」的功能，將計量與記錄路由傳送至不同的位置。 下列步驟示範如何使用新的「Azure 監視器 」資料接收器，使用 Resource Manager 範本與 PowerShell 來部署 VM。

## <a name="author-resource-manager-template"></a>製作 Resource Manager 範本
針對此範例，您可以使用公開提供的範例範本。 起始範本位於 https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows。

- **Azuredeploy.json** 是預先設定的 Resource Manager 範本，可用於部署虛擬機器。

- **Azuredeploy.parameters.json** 是參數檔案，儲存您要為 VM 設定哪些使用者名稱與密碼等的資訊。 在部署期間，Resource Manager 範本會使用此檔案中設定的參數。

下載這兩個檔案並儲存在本機。

### <a name="modify-azuredeployparametersjson"></a>修改 azuredeploy.parameters.json
開啟*azuredeploy.parameters.json」檔案。*

1. 輸入 VM 的 **adminUsername** 與 **adminPassword** 值。 這些參數可用來從遠端存取 VM。 若要避免該 VM 被駭，請勿使用此範本中的值。 Bot 會掃描網際網路，取得公用 GitHub 存放庫中的使用者名稱與密碼。 很可能會利用這些預設值來測試 VM。

1. 為 VM 建立唯一的 DNS 名稱。

### <a name="modify-azuredeployjson"></a>修改 azuredeploy.json

開啟 *azuredeploy.json* 檔案

在 StorageAccountName 的專案之後，將儲存體帳戶識別碼新增至範本的**variables**區段 **。**

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

將此受控服務識別（MSI）擴充功能新增至範本的**資源**區段頂端。 此擴充功能可確保 Azure 監視器接受所發出的計量。

```json
//Find this code.
"resources": [
// Add this code directly below.
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'), '/', 'WADExtensionSetup')]",
        "apiVersion": "2017-12-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    },
```

將 **identity** 設定新增至 VM 資源，確保 Azure 會將系統身分識別指定給 MSI 擴充功能。 這個步驟可確保 VM 能對 Azure 監視器發出與本身相關的客體計量。

```json
// Find this section
                "subnet": {
            "id": "[variables('subnetRef')]"
            }
        }
        }
    ]
    }
},
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    // add these 3 lines below
    "identity": {
    "type": "SystemAssigned"
    },
    //end of added lines
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
    "hardwareProfile": {
    ...
```

新增下列設定以在 Windows 虛擬機器上啟用診斷擴充功能。 針對以資源管理員為基礎的簡單虛擬機器，我們能將擴充功能設定新增至虛擬機器的資源陣列。 "sinks"&mdash; "AzMonSink" 一行與本節稍後的對應 "SinksConfig"&mdash;可讓擴充功能直接對 Azure 監視器發出計量。 請視需要新增或移除效能計數器。


```json
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
    "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob]"
    }
}
},
//Start of section to add
"resources": [
{
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'), '/', 'Microsoft.Insights.VMDiagnosticsSettings')]",
            "apiVersion": "2017-12-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.12",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "WadCfg": {
                "DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
        },
                    "Directories": {
                    "scheduledTransferPeriod": "PT1M",
    "IISLogs": {
                        "containerName": "wad-iis-logfiles"
                    },
                    "FailedRequestLogs": {
                        "containerName": "wad-failedrequestlogs"
                    }
                    },
                    "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "sinks": "AzMonSink",
                    "PerformanceCounterConfiguration": [
                        {
                        "counterSpecifier": "\\Memory\\Available Bytes",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\Committed Bytes",
                        "sampleRate": "PT15S"
                        }
                    ]
                    },
                    "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                        {
                        "name": "Application!*"
                        }
                    ]
                    },
                    "Logs": {
                    "scheduledTransferPeriod": "PT1M",
                    "scheduledTransferLogLevelFilter": "Error"
                    }
                },
                "SinksConfig": {
                    "Sink": [
                    {
                        "name" : "AzMonSink",
                        "AzureMonitor" : {}
                    }
                    ]
                }
                },
                "StorageAccount": "[variables('storageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[variables('storageAccountName')]",
                "storageAccountKey": "[listKeys(variables('accountid'),'2015-06-15').key1]",
                "storageAccountEndPoint": "https://core.windows.net/"
            }
            }
        }
        ]
//End of section to add
```


儲存並關閉這兩個檔案。


## <a name="deploy-the-resource-manager-template"></a>部署 Resource Manager 範本

> [!NOTE]
> 您必須執行 Azure 診斷擴充功能1.5 或更高版本，並在您的 Resource Manager 範本中將**autoUpgradeMinorVersion**：屬性設定為 ' true '。 接著，Azure 會在啟動 VM 時載入適當的擴充功能。 如果您的範本中沒有這些設定，請進行變更，並重新部署該範本。


我們會利用 Azure PowerShell 來部署 Resource Manager 範本。

1. 啟動 PowerShell。
1. 使用 `Login-AzAccount` 登入 Azure。
1. 使用 `Get-AzSubscription` 取得訂用帳戶清單。
1. 設定您用來建立/更新虛擬機器的訂用帳戶：

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. 若要為部署的 VM 建立新的資源群組，請執行下列命令：

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > 請記得[使用已針對自訂計量啟用的 Azure 區域](metrics-custom-overview.md)。

1. 執行下列命令以使用 Resource Manager 範本部署 VM。
   > [!NOTE]
   > 如果您想要更新現有 VM，只要將 *-Mode Incremental* 新增至下列命令的結尾。

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. 部署成功之後，VM 應位於 Azure 入口網站中，向 Azure 監視器發出計量。

   > [!NOTE]
   > 您可能會遇到和所選 vmSkuSize 相關的錯誤。 如果發生這種情況，請回到 azuredeploy.json 檔案，然後更新 vmSkuSize 參數的預設值。 在此案例中，建議您嘗試 "Standard_DS1_v2"。

## <a name="chart-your-metrics"></a>繪製計量圖表

1. 登入 Azure 管理入口網站。

2. 在左側功能表上，選取 [監視]****。

3. 在 [監視] 頁面上，選取 [計量]****。

   ![[計量] 頁面](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. 將彙總期間變更為 [過去 30 分鐘]****。

5. 在 [資源] 下拉式功能表中，選取您所建立的 VM。 如果您沒有變更範本中的名稱，它應該是 *SimpleWinVM2*。

6. 在 [命名空間] 下拉式功能表中，選取 **azure.vm.windows.guest**

7. 在 [計量] 下拉式功能表中，選取 [記憶體\%認可的位元組 (使用中)]****。


## <a name="next-steps"></a>後續步驟
- 深入了解[自訂計量](metrics-custom-overview.md)。

