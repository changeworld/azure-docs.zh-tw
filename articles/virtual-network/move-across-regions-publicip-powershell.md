---
title: 使用 Azure PowerShell 將 Azure 公用 IP 設定移至另一個 Azure 區域
description: 利用 Azure Resource Manager 範本以使用 Azure PowerShell 將 Azure 公用 IP 設定從某個 Azure 區域移至另一個區域。
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 0a3fdb776643e2cf817c50fb9b716f7315151e21
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223392"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-azure-powershell"></a>使用 Azure PowerShell 將 Azure 公用 IP 設定移至另一個區域

有許多情況會讓您想要將現有的 Azure 公用 IP 設定從某個區域移至另一個。 例如，您可能會希望使用相同的設定和 SKU 來建立公用 IP 以進行測試。 基於災害復原規劃，您可能也會想要將公用 IP 設定移至另一個區域。

**Azure 公用 IP 特定於區域，且無法從某個區域移至另一個區域。** 不過，您可使用 Azure Resource Manager 範本來匯出公用 IP 的現有設定。  接著，您可將公用 IP 匯出至範本、修改參數以符合目的地區域，然後將範本部署到新的區域，藉此將資源放在另一個區域中。  如需 Resource Manager 和範本的詳細資訊，請參閱[將資源群組匯出至範本](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates) (機器翻譯)


## <a name="prerequisites"></a>必要條件

- 請確定 Azure 公用 IP 確實位在想要從其移動的 Azure 區域中。

- Azure 公用 IP 無法在區域之間移動。  您必須將新公用 IP 與目標區域中的資源建立關聯。

- 若要匯出公用 IP 設定並部署範本以在另一個區域中建立公用 IP，則需要網路參與者角色或權限更高的角色。
   
- 識別來源網路配置，以及您目前使用的所有資源。 此配置包括 (但不限於) 負載平衡器、網路安全性群組 (NSG) 及虛擬網路。

- 請驗證 Azure 訂用帳戶允許在所使用目標區域中建立 VM。 請連絡支援人員啟用所需的配額。

- 請確認訂用帳戶具有足夠資源，能支援新增公用 IP 以用於此程序。  請參閱 [Azure 訂用帳戶和服務限制、配額與限制](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)。


## <a name="prepare-and-move"></a>準備及移動
下列步驟示範如何使用 Resource Manager 範本來準備公用 IP 以進行設定移動，並使用 Azure PowerShell 將公用 IP 設定移至目標區域。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>匯出範本並從指令碼部署

1. 使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 命令登入 Azure 訂用帳戶，並遵循畫面上的指示操作：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 取得想要移至目標區域的公用 IP 資源識別碼，並使用 [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0) 將其放在變數中：

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 將來源虛擬網路匯出至執行 [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0) 命令其目錄中的 .json 檔案：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. 下載檔案會以匯出資源的來源資源群組命名。  找出從命令匯出名為 **\<resource-group-name>.json** 的檔案，然後在所選編輯器中將其開啟：
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. 若要編輯公用 IP 名稱的參數，請將來源公用 IP 名稱的 **defaultValue** 屬性變更為目標公用 IP 名稱，並確認名稱以引號括住：
    
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

6. 若要編輯公用 IP 將移至的目標區域，請在資源下方變更 **位置** 屬性：

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
  
7. 若要取得區域位置代碼，您可執行下列命令來使用 Azure PowerShell Cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. 您也可以根據需求而選擇變更範本中的其他參數：

    * **SKU** - 您可變更 **\<resource-group-name>.json** 檔案中的 **SKU** > **名稱** 屬性，以將設定中的公用 IP SKU 從標準變更為基本，或從基本變更為標準：

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

         如需基本和標準 SKU 公用 IP 之間差異的詳細資訊，請參閱[建立、變更或刪除公用 IP 位址](./virtual-network-public-ip-address.md) (機器翻譯)。

    * **公用 IP 配置方法** 和 **閒置逾時** - 您可將 **publicIPAllocationMethod** 屬性從 **動態** 變更為 **靜態**，或從 **靜態** 變更為 **動態**，以在範本中變更這兩個選項。 將 **idleTimeoutInMinutes** 屬性變更為所要的數量，即可變更閒置時間。  預設值為 **4**：

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

        如需配置方法和閒置逾時值的詳細資訊，請參閱[建立、變更或刪除公用 IP 位址](./virtual-network-public-ip-address.md) (機器翻譯)。


9. 儲存 **\<resource-group-name>.json** 檔案。

10. 在目標區域中建立資源群組，以使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) 來部署目標公用 IP。
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. 使用 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)，將已編輯 **\<resource-group-name>.json** 檔案部署至上一個步驟中建立的資源群組：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 若要驗證資源已在目標區域中建立，請使用 [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) 和 [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```
## <a name="discard"></a>捨棄 

部署後，如果希望在目標中重新開始或捨棄公用 IP，請刪除在目標中建立的資源群組，這將會刪除已移動的公用 IP。  若要移除資源群組，請使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>清除

若要認可變更並完成移動虛擬網路、刪除來源虛擬網路或資源群組，請使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) 或 [Remove-AzPublicIPAddress](/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將 Azure 公用 IP 從某區域移至另一個區域，並清除了來源資源。  若要深入了解如何在 Azure 中的區域之間移動資源和災害復原，請參閱：


- [將資源移至新的資源群組或訂用帳戶](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [將 Azure VM 移至其他區域](../site-recovery/azure-to-azure-tutorial-migrate.md)