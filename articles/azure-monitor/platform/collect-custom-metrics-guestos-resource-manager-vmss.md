---
title: 使用範本在 Azure 監視器中收集 Windows 擴展集計量
description: 使用 Windows 虛擬機器擴展集的 Resource Manager 範本將客體作業系統計量傳送至 Azure 監視器計量存放區
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 76f73df01b34cb20be48aefa3b5b3a6392a35b8b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87045197"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>使用 Windows 虛擬機器擴展集的 Azure Resource Manager 範本將客體作業系統計量傳送至 Azure 監視器計量存放區

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

您可以使用 Azure 監視器的 [Windows Azure 診斷 (WAD) 擴充功能](diagnostics-extension-overview.md)，從當作虛擬機器、雲端服務或 Azure Service Fabric 叢集一部分執行的客體作業系統 (客體 OS) 收集計量與記錄。 擴充功能可以將遙測資料傳送到先前連結的文章中所列的許多不同位置。  

此文章說明將 Windows 虛擬機器擴展集的客體作業系統效能計量傳送至 Azure 監視器資料存放區的程序。 從 Windows Azure 診斷 1.11 版開始，您可以直接將計量寫入到已收集標準平台計量的 Azure 監視器計量存放區。 藉由將計量儲存在此位置，您就可以存取平台計量適用的相同動作。 動作包括近乎即時的警示、圖表、路由、從 REST API 存取以及更多功能。 在過去，Windows Azure 診斷擴充功能會寫入到 Azure 儲存體，而不是 Azure 監視器資料存放區。  

如果您剛開始使用 Resource Manager 範本，請了解[範本部署](../../azure-resource-manager/management/overview.md)與其結構和語法。  

## <a name="prerequisites"></a>Prerequisites

- 您必須先向 [Microsoft.Insights](../../azure-resource-manager/management/resource-providers-and-types.md) 註冊您的訂用帳戶。 

- 您需要安裝 [Azure PowerShell](/powershell/azure)，或可以使用 [Azure Cloud Shell](../../cloud-shell/overview.md)。 

- 您的 VM 資源必須位於[支援自訂計量的區域](metrics-custom-overview.md#supported-regions)中。

## <a name="set-up-azure-monitor-as-a-data-sink"></a>設定 Azure 監視器作為資料接收器 
Azure 診斷擴充功能會使用稱為「 **資料接收** 」的功能，將計量和記錄路由至不同的位置。 下列步驟示範如何使用新的「Azure 監視器 」資料接收器，使用 Resource Manager 範本與 PowerShell 來部署 VM。 

## <a name="author-a-resource-manager-template"></a>撰寫 Resource Manager 範本 
在此範例中，您可以使用公開可用的 [範例範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale)：  

- **Azuredeploy.json** 是預先設定的 Resource Manager 範本，可用於部署虛擬機器擴展集。

- **Azuredeploy.parameters.json** 是參數檔案，儲存您要為 VM 設定的使用者名稱與密碼等資訊。 在部署期間，Resource Manager 範本會使用此檔案中設定的參數。 

下載這兩個檔案並儲存在本機。 

###  <a name="modify-azuredeployparametersjson"></a>修改 azuredeploy.parameters.json
開啟檔案 ** 上的azuredeploy.parameters.js** ：  
 
- 提供您想要部署的 **vmSKU**。 建議使用 Standard_D2_v3。 
- 指定您要用於虛擬機器擴展集的 **windowsOSVersion**。 建議使用 2016-Datacenter。 
- 使用 **vmssName** 屬性為要部署的虛擬機器擴展集資源命名。 例如，**VMSS-WAD-TEST**。    
- 使用 **instanceCount** 屬性，指定您要在虛擬機器擴展集上執行的 VM 數目。
- 輸入虛擬機器擴展集的 **adminUsername** 與 **adminPassword** 值。 這些參數可用來從遠端存取擴展集中的 VM。 為避免 VM 被駭，**請勿**使用此範本中的資訊。 Bot 會掃描網際網路，取得公用 GitHub 存放庫中的使用者名稱與密碼。 使用這些預設值的 VM 可能是測試 VM。 


###  <a name="modify-azuredeployjson"></a>修改 azuredeploy.json
開啟檔案 ** 上的azuredeploy.js** 。 

新增變數來儲存 Resource Manager 範本中的儲存體帳戶資訊。 診斷設定檔中指定的任何記錄或效能計數器，都會寫入 Azure 監視器計量存放區和此處指定的儲存體帳戶： 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
在 [資源] 區段中尋找虛擬機器擴展集定義，然後將 [身分 **識別** ] 區段新增至設定。 此新增作業可確保 Azure 對其指派系統身分識別。 這個步驟也可確保擴展集中的 VM 可對 Azure 監視器發出與本身相關的客體計量：  

```json
    { 
      "type": "Microsoft.Compute/virtualMachineScaleSets", 
      "name": "[variables('namingInfix')]", 
      "location": "[resourceGroup().location]", 
      "apiVersion": "2017-03-30", 
      //add these lines below
      "identity": { 
           "type": "systemAssigned" 
       }, 
       //end of lines to add
```

在虛擬機器擴展集資源中，找到 **virtualMachineProfile** 區段。 新增名為 **extensionsProfile** 的新設定檔來管理擴充功能。  


在 **extensionProfile** 中，將新擴充功能新增至範本，如 **VMSS-WAD-extension** 區段中所示。  此節是 Azure 資源受控識別的擴充功能，可確保 Azure 監視器會接受所發出的計量。 **name** 欄位可以包含任何名稱。 

MSI 擴充功能中的下列程式碼也可以將診斷擴充功能與設定作為擴充功能資源，新增至虛擬機器擴展集資源。 請視需要新增或移除效能計數器： 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identities for Azure resources   
                { 
                 "name": "VMSS-WAD-extension", 
                 "properties": { 
                       "publisher": "Microsoft.ManagedIdentity", 
                       "type": "ManagedIdentityExtensionForWindows", 
                       "typeHandlerVersion": "1.0", 
                       "autoUpgradeMinorVersion": true, 
                       "settings": { 
                             "port": 50342 
                           }, 
                       "protectedSettings": {} 
                     } 
                                
            }, 
            // add diagnostic extension. (Remove this comment after pasting.)
            { 
              "name": "[concat('VMDiagnosticsVmExt','_vmNodeType0Name')]", 
              "properties": { 
                   "type": "IaaSDiagnostics", 
                   "autoUpgradeMinorVersion": true, 
                   "protectedSettings": { 
                        "storageAccountName": "[variables('storageAccountName')]", 
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2015-05-01-preview').key1]", 
                        "storageAccountEndPoint": "https://core.windows.net/" 
                   }, 
                   "publisher": "Microsoft.Azure.Diagnostics", 
                   "settings": { 
                        "WadCfg": { 
                              "DiagnosticMonitorConfiguration": { 
                                   "overallQuotaInMB": "50000", 
                                   "PerformanceCounters": { 
                                       "scheduledTransferPeriod": "PT1M", 
                                       "sinks": "AzMonSink", 
                                       "PerformanceCounterConfiguration": [ 
                                          { 
              "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                                              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Available Bytes", 
              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Committed Bytes", 
              "sampleRate": "PT15S" 
           } 
                                       ] 
                                 }, 
                                 "EtwProviders": { 
                                       "EtwEventSourceProviderConfiguration": [ 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Actors", 
                                              "scheduledTransferKeywordFilter": "1", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                              "eventDestination": "ServiceFabricReliableActorEventTable" 
                                           } 
                                           }, 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Services", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricReliableServiceEventTable" 
                                              } 
                                           } 
                                     ], 
                                     "EtwManifestProviderConfiguration": [ 
                                           { 
                                              "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8", 
                                              "scheduledTransferLogLevelFilter": "Information", 
                                              "scheduledTransferKeywordFilter": "4611686018427387904", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricSystemEventTable" 
                                              } 
                                          } 
                                     ] 
                               } 
                               }, 
                               "SinksConfig": { 
                                     "Sink": [ 
                                          { 
                                              "name": "AzMonSink", 
                                              "AzureMonitor": {} 
                                          } 
                                      ] 
                               } 
                         }, 
                         "StorageAccount": "[variables('storageAccountName')]" 
                         }, 
                        "typeHandlerVersion": "1.11" 
                  } 
           } 
            ] 
          }
          }
      }
    },
    //end of added code plus a few brackets. Be sure that the number and type of brackets match properly when done. 
    {
      "type": "Microsoft.Insights/autoscaleSettings",
...
```


為儲存體帳戶新增 **dependsOn** ，以確保它會以正確的順序建立： 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

如果尚未在範本中建立儲存體帳戶，請予以建立： 

```json
"resources": [
// add this code    
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
    "accountType": "Standard_LRS"
    }
},
// end added code
{ 
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
```

儲存並關閉這兩個檔案。 

## <a name="deploy-the-resource-manager-template"></a>部署 Resource Manager 範本 

> [!NOTE]  
> 您必須執行 Azure 診斷延伸模組版本1.5 或更高版本 **，並** 在 Resource Manager 範本中將 [ **autoUpgradeMinorVersion：** ] 屬性設定為 [ **true** ]。 接著，Azure 會在啟動 VM 時載入適當的擴充功能。 如果您的範本中沒有這些設定，請進行變更，並重新部署該範本。 


請使用 Azure PowerShell 來部署 Resource Manager 範本：  

1. 啟動 PowerShell。 
1. 使用 `Login-AzAccount` 登入 Azure。
1. 使用 `Get-AzSubscription` 取得訂用帳戶清單。
1. 設定您要建立的訂用帳戶，或更新虛擬機器： 

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. 為要部署的 VM 建立新的資源群組。 執行下列命令： 

   ```powershell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > 請記得，使用為自訂計量啟用的 Azure 區域。 請記得，使用[為自訂計量啟用的 Azure 區域](./metrics-custom-overview.md#supported-regions)。
 
1. 執行下列命令來部署 VM：  

   > [!NOTE]  
   > 如果您想要更新現有擴展集，請將 **-Mode Incremental** 新增至命令的結尾。 
 
   ```powershell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. 部署成功之後，您應該會在 Azure 入口網站中發現虛擬機器擴展集。 它應該會對 Azure 監視器發出計量。 

   > [!NOTE]  
   > 您可能會遇到有關所選 **vmSkuSize**的錯誤。 在該狀況下，請回到 **azuredeploy.json** 檔案，然後更新 **vmSkuSize** 參數的預設值。 我們建議您嘗試使用 **Standard_DS1_v2**。 


## <a name="chart-your-metrics"></a>繪製計量圖表 

1. 登入 Azure 入口網站。 

1. 在左側功能表中，選取 [監視]****。 

1. 在 [ **監視** ] 頁面上選取 [ **計量**]。 

   ![監視 - 計量頁面](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. 將彙總期間變更為 [過去 30 分鐘]。  

1. 在 [資源] 下拉式功能表中，選取您建立的虛擬機器擴展集。  

1. 在 [命名空間] 下拉式功能表中，選取 [ **azure**]。 

1. 在 [計量] 下拉式功能表中，選取 [記憶體\%認可的位元組 (使用中)\]****。  

接著，您也可以選擇使用這個計量上的維度，為特定 VM 繪製圖表，或對擴展集中的每個 VM 繪製圖表。 



## <a name="next-steps"></a>後續步驟
- 深入了解[自訂計量](metrics-custom-overview.md)。
