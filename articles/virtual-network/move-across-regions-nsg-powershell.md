---
title: 使用 Azure PowerShell 將 Azure 網路安全性群組 （NSG） 移動到其他 Azure 區域
description: 使用 Azure 資源管理器範本使用 Azure PowerShell 將 Azure 網路安全性群組從一個 Azure 區域移動到另一個 Azure 區域。
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 0cbd8f61cb1b4cb8eae6b30625fb3039ff75adde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641463"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>使用 Azure PowerShell 將 Azure 網路安全性群組 （NSG） 移動到其他區域

在各種方案中，您希望將現有 NSG 從一個區域移動到另一個區域。 例如，您可能希望創建具有相同配置和安全規則的 NSG 進行測試。 作為災害復原規劃的一部分，您可能還希望將 NSG 移動到其他區域。

Azure 安全性群組不能從一個區域移動到另一個區域。 但是，可以使用 Azure 資源管理器範本匯出 NSG 的現有配置和安全規則。  然後，您可以通過將 NSG 匯出到範本、修改參數以匹配目的地區域，然後將範本部署到新區域，在另一個區域中暫按資源。  有關資源管理器和範本的詳細資訊，請參閱[將資源組匯出到範本](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)。


## <a name="prerequisites"></a>Prerequisites

- 確保 Azure 網路安全性群組位於要從其移動的 Azure 區域中。

- Azure 網路安全性群組不能在區域之間移動。  您必須將新的 NSG 與目的地區域中的資源相關聯。

- 要匯出 NSG 配置並部署範本以在另一個區域中創建 NSG，您需要網路參與者角色或更高版本。
   
- 識別來源網路配置，以及您目前使用的所有資源。 此佈局包括但不限於負載等化器、公共 IP 和虛擬網路。

- 驗證 Azure 訂閱是否允許在使用的目的地區域中創建 NSG。 請連絡支援人員啟用所需的配額。

- 請確保您的訂閱有足夠的資源來支援此流程的 NSG 添加。  請參閱 [Azure 訂用帳戶和服務限制、配額與限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。


## <a name="prepare-and-move"></a>準備和移動
以下步驟演示如何使用資源管理器範本為配置和安全規則移動準備網路安全性群組，並使用 Azure PowerShell 將 NSG 配置和安全規則移動到目的地區域。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>匯出範本並從腳本中部署

1. 使用[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令登錄到 Azure 訂閱，並按照螢幕上的說明操作：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 獲取要移動到目的地區域的 NSG 的資源識別碼，並將其放在使用[Get-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0)的變數中：

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 將源 NSG 匯出到 .json 檔到執行命令[Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)的目錄中：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. 下載的檔將以資源匯出的資源組命名。  查找從名為"**\<資源組名稱>.json**的命令匯出的檔，並在您選擇的編輯器中打開該檔：
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. 要編輯 NSG 名稱的參數，請將源 NSG 名稱的屬性**預設值**更改為目標 NSG 的名稱，請確保名稱以引號形式：
    
    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
            "defaultValue": "<target-nsg-name>",
            "type": "String"
            }
        }

    ```


6. 要編輯將移動 NSG 配置和安全規則的目的地區域，請更改**資源**下**的位置**屬性：

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78", 
             }
            }
    ```
  
7. 要獲取區域位置代碼，可以使用 Azure PowerShell Cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)運行以下命令：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. 如果願意，還可以更改**\<資源組名稱>.json**中的其他參數，並且根據您的要求是可選的：

    * **安全規則**- 您可以通過向**\<資源組名稱>.json**檔中**的安全規則**部分添加或刪除規則，編輯哪些規則部署到目標 NSG：

        ```json
           "resources": [
                  {
                  "type": "Microsoft.Network/networkSecurityGroups",
                  "apiVersion": "2019-06-01",
                  "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
                  "location": "TARGET REGION",
                  "properties": {
                       "provisioningState": "Succeeded",
                       "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                  "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                             "provisioningState": "Succeeded",
                             "protocol": "TCP",
                             "sourcePortRange": "*",
                             "destinationPortRange": "3389",
                             "sourceAddressPrefix": "*",
                             "destinationAddressPrefix": "*",
                             "access": "Allow",
                             "priority": 300,
                             "direction": "Inbound",
                             "sourcePortRanges": [],
                             "destinationPortRanges": [],
                             "sourceAddressPrefixes": [],
                             "destinationAddressPrefixes": []
                            }
                        ]
            }  
            
        ```

        要完成目標 NSG 中規則的添加或刪除，還必須以以下示例的格式編輯**\<資源組名稱>.json**檔末尾的自訂規則類型：

        ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
        ```

9. 保存**\<資源組名稱>.json**檔。

10. 在目的地區域中創建資源組，以便使用[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)部署目標 NSG ：
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. 使用[New-AzResourceGroup 部署](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)將編輯**\<的資源組名稱>.json**檔部署到上一步中創建的資源組：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 要驗證在目的地區域中創建的資源，請使用[Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0)和[Get-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>捨棄 

部署後，如果要重新開始或丟棄目標中的 NSG，請刪除在目標中創建的資源組，並將刪除移動的 NSG。  要刪除資源組，請使用[刪除 AzResource 組](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>清除

要提交更改並完成 NSG 的移動，請刪除源 NSG 或資源組，使用[刪除 AzResource 組](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)或刪除[AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworksecuritygroup?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>後續步驟

在本教程中，您將 Azure 網路安全性群組從一個區域移動到另一個區域並清理了源資源。  要瞭解有關在 Azure 中在區域和災害復原之間移動資源的詳細資訊，請參閱：


- [將資源移至新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [將 Azure VM 移至其他區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
