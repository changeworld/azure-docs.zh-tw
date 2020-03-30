---
title: 使用 Azure PowerShell 將 Azure 公共 IP 移動到其他 Azure 區域
description: 使用 Azure 資源管理器範本使用 Azure PowerShell 將 Azure 公共 IP 從一個 Azure 區域移動到另一個 Azure 區域。
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: c55b6011381d385fed7c7b8175ff02ec9be66fdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641532"
---
# <a name="move-azure-public-ip-to-another-region-using-azure-powershell"></a>使用 Azure PowerShell 將 Azure 公共 IP 移動到其他區域

在各種方案中，您希望將現有的 Azure 公共 IP 從一個區域移動到另一個區域。 例如，您可能希望創建具有相同配置和 sKU 以進行測試的公共 IP。 作為災害復原規劃的一部分，您可能還希望將公共 IP 移動到其他區域。

Azure 公共 IP 特定于區域，不能從一個區域移動到另一個區域。 但是，可以使用 Azure 資源管理器範本匯出公共 IP 的現有配置。  然後，可以通過將公共 IP 匯出到範本、修改參數以匹配目的地區域，然後將範本部署到新區域，在另一個區域中暫按資源。  有關資源管理器和範本的詳細資訊，請參閱[將資源組匯出到範本](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Prerequisites

- 確保 Azure 公共 IP 位於要從其移動的 Azure 區域中。

- Azure 公共 IP 不能在區域之間移動。  您必須將新的公共 IP 與目的地區域中的資源相關聯。

- 要匯出公共 IP 配置並部署範本以在另一個區域創建公共 IP，您需要網路參與者角色或更高版本。
   
- 識別來源網路配置，以及您目前使用的所有資源。 此佈局包括但不限於負載等化器、網路安全性群組 （NSG） 和虛擬網路。

- 驗證 Azure 訂閱是否允許您在使用的目的地區域中創建公共 IP。 請連絡支援人員啟用所需的配額。

- 請確保您的訂閱有足夠的資源來支援為此過程添加公共 IP。  請參閱 [Azure 訂用帳戶和服務限制、配額與限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。


## <a name="prepare-and-move"></a>準備和移動
以下步驟演示如何使用資源管理器範本為配置移動準備公共 IP，並使用 Azure PowerShell 將公共 IP 配置移動到目的地區域。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>匯出範本並從腳本中部署

1. 使用[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令登錄到 Azure 訂閱，並按照螢幕上的說明操作：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 獲取要移動到目的地區域的公共 IP 的資源識別碼，並使用[Get-AzPublicIP 位址](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)將其放置在變數中：

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 將源虛擬網路匯出到 .json 檔到執行命令[Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)的目錄中：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. 下載的檔將以資源匯出的資源組命名。  查找從名為"**\<資源組名稱>.json**的命令匯出的檔，並在您選擇的編輯器中打開該檔：
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. 要編輯公共 IP 名稱的參數，請將源公共 IP 名稱的屬性**預設值**更改為目標公共 IP 的名稱，請確保名稱以引號形式：
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. 要編輯將移動公共 IP 的目的地區域，請更改資源下**的位置**屬性：

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. 要獲取區域位置代碼，可以使用 Azure PowerShell Cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)運行以下命令：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. 如果願意，還可以更改範本中的其他參數，並且根據您的要求是可選的：

    * **SKU** - 您可以通過更改**\<資源組名稱>.json**檔中的 sku**名稱**屬性，將配置中的公共 IP 的**sKU** > 從標準更改為基本或基本到標準：

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         有關基本和標準 sku 公共 ip 之間的差異的詳細資訊，請參閱[創建、更改或刪除公共 IP 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。

    * **公共 IP 分配方法和****空閒超時**- 您可以通過將**公共 IP 分配方法**屬性從 **"動態**"更改為 **"靜態****"更改為****"靜態**"來更改範本中的這兩個選項。 可以通過將**idleTimeoutIn分鐘**屬性更改為所需量來更改空閒超時。  預設值為**4**：

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        有關分配方法和空閒超時值的詳細資訊，請參閱[創建、更改或刪除公共 IP 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。


9. 保存**\<資源組名稱>.json**檔。

10. 在目的地區域中創建一個資源組，以便使用[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)部署的目標公共 IP。
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. 使用[New-AzResourceGroup 部署](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)將編輯**\<的資源組名稱>.json**檔部署到上一步中創建的資源組：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 要驗證在目的地區域中創建的資源，請使用[Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0)和[Get-AzPublicIP 位址](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```
## <a name="discard"></a>捨棄 

部署後，如果要重新開始或丟棄目標中的公共 IP，請刪除在目標中創建的資源組，並將刪除移動的公共 IP。  要刪除資源組，請使用[刪除 AzResource 組](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>清除

要提交更改並完成虛擬網路的移動，請刪除源虛擬網路或資源組，使用[刪除 AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)或刪除[AzPublicIP 位址](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>後續步驟

在本教程中，您將 Azure 公共 IP 從一個區域移動到另一個區域並清理了源資源。  要瞭解有關在 Azure 中在區域和災害復原之間移動資源的詳細資訊，請參閱：


- [將資源移至新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [將 Azure VM 移至其他區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
