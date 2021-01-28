---
title: 使用 Azure PowerShell 將 Azure 網路安全性群組 (NSG) 移至另一個 Azure 區域
description: 使用 Azure Resource Manager 範本，使用 Azure PowerShell 將 Azure 網路安全性群組從一個 Azure 區域移至另一個區域。
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: ad73ef03aa9623fb724f1397697fac18f659a90c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98934985"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>使用 Azure PowerShell 將 Azure 網路安全性群組 (NSG) 移至另一個區域

在許多情況下，您會想要將現有的 Nsg 從一個區域移到另一個區域。 例如，您可能會想要使用相同的設定和安全性規則來建立 NSG 來進行測試。 您也可能想要將 NSG 移至另一個區域，作為損毀修復計畫的一部分。

Azure 安全性群組無法從一個區域移至另一個區域。 不過，您可以使用 Azure Resource Manager 範本來匯出 NSG 的現有設定和安全性規則。  然後，您可以將 NSG 匯出至範本、修改參數以符合目的地區域，然後將範本部署到新的區域，以暫存另一個區域中的資源。  如需 Resource Manager 和範本的詳細資訊，請參閱 [將資源群組匯出至範本](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)。


## <a name="prerequisites"></a>先決條件

- 請確定 Azure 網路安全性群組位於您要移動的 Azure 區域中。

- 無法在區域之間移動 Azure 網路安全性群組。  您必須將新的 NSG 與目的地區域中的資源產生關聯。

- 若要匯出 NSG 設定並部署範本，以在另一個區域中建立 NSG，您需要「網路參與者」角色或更高的角色。
   
- 識別來源網路配置，以及您目前使用的所有資源。 此版面配置包含但不限於負載平衡器、公用 Ip 及虛擬網路。

- 確認您的 Azure 訂用帳戶可讓您在使用的目的地區域中建立 Nsg。 請連絡支援人員啟用所需的配額。

- 請確定您的訂用帳戶有足夠的資源，可支援在此程式中加入 Nsg。  請參閱 [Azure 訂用帳戶和服務限制、配額與限制](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)。


## <a name="prepare-and-move"></a>準備及移動
下列步驟說明如何使用 Resource Manager 範本準備設定和安全性規則移動的網路安全性群組，並使用 Azure PowerShell 將 NSG 設定和安全性規則移至目的地區域。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>匯出範本並從指令碼部署

1. 使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 命令登入 Azure 訂用帳戶，並遵循畫面上的指示操作：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 取得您想要移至目的地區域之 NSG 的資源識別碼，並將其放在使用 [>new-aznetworksecuritygroup](/powershell/module/az.network/get-aznetworksecuritygroup)的變數中：

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 將來源 NSG 匯出至 json 檔案，並將其匯出至您執行 [>new-azresourcegroup](/powershell/module/az.resources/export-azresourcegroup)命令的目錄：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. 下載檔案會以匯出資源的來源資源群組命名。  找出從命令匯出名為 **\<resource-group-name>.json** 的檔案，然後在所選編輯器中將其開啟：
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. 若要編輯 NSG 名稱的參數，請將來源 NSG 名稱的屬性 **defaultValue** 變更為目標 NSG 的名稱，並確定名稱是以引號括住：
    
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


6. 若要編輯 NSG 設定和安全性規則將移至其中的目的地區域，請變更 [**資源**] 底下的 [**位置**] 屬性：

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
  
7. 若要取得區域位置代碼，您可執行下列命令來使用 Azure PowerShell Cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation)：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. 您也可以視需要變更 **\<resource-group-name> json** 中的其他參數，並選擇性地視您的需求而定：

    * **安全性規則**-您可以藉由在 **\<resource-group-name> json** 檔案的 **securityRules** 區段中新增或移除規則，來編輯要部署到目標 NSG 的規則：

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

        若要完成新增或移除目標 NSG 中的規則，您也必須以下列範例的格式，在 **\<resource-group-name> json** 檔案的結尾編輯自訂規則類型：

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

9. 儲存 **\<resource-group-name>.json** 檔案。

10. 在目的地區域中建立資源群組，以使用 [新的->new-azresourcegroup](/powershell/module/az.resources/new-azresourcegroup)部署目標 NSG：
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. 使用 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)，將已編輯 **\<resource-group-name>.json** 檔案部署至上一個步驟中建立的資源群組：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 若要確認已在目的地區域中建立資源，請使用 [>new-azresourcegroup](/powershell/module/az.resources/get-azresourcegroup) 和 [>new-aznetworksecuritygroup](/powershell/module/az.network/get-aznetworksecuritygroup)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>捨棄 

部署之後，如果您想要重新開機或捨棄目標中的 NSG，請刪除在目標中建立的資源群組，並刪除已移動的 NSG。  若要移除資源群組，請使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>清除

若要認可變更並完成 NSG 的移動，請刪除來源 NSG 或資源群組、使用 [>new-azresourcegroup](/powershell/module/az.resources/remove-azresourcegroup) 或 [移除->new-aznetworksecuritygroup](/powershell/module/az.network/remove-aznetworksecuritygroup)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將 Azure 網路安全性群組從一個區域移至另一個區域，並清除了來源資源。  若要深入了解如何在 Azure 中的區域之間移動資源和災害復原，請參閱：


- [將資源移至新的資源群組或訂用帳戶](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [將 Azure VM 移至其他區域](../site-recovery/azure-to-azure-tutorial-migrate.md)
