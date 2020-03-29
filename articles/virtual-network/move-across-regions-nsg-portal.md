---
title: 使用 Azure 門戶將 Azure 網路安全性群組 （NSG） 移動到其他 Azure 區域
description: 使用 Azure 資源管理器範本使用 Azure 門戶將 Azure 網路安全性群組從一個 Azure 區域移動到另一個 Azure 區域。
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: dce267178c3caf813ccdcac4bba86ccfde3f3421
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647181"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>使用 Azure 門戶將 Azure 網路安全性群組 （NSG） 移動到其他區域

在各種方案中，您希望將現有 NSG 從一個區域移動到另一個區域。 例如，您可能希望創建具有相同配置和安全規則的 NSG 進行測試。 作為災害復原規劃的一部分，您可能還希望將 NSG 移動到其他區域。

Azure 安全性群組不能從一個區域移動到另一個區域。 但是，可以使用 Azure 資源管理器範本匯出 NSG 的現有配置和安全規則。  然後，您可以通過將 NSG 匯出到範本、修改參數以匹配目的地區域，然後將範本部署到新區域，在另一個區域中暫按資源。  有關資源管理器和範本的詳細資訊，請參閱[快速入門：使用 Azure 門戶創建和部署 Azure 資源管理器範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。


## <a name="prerequisites"></a>Prerequisites

- 確保 Azure 網路安全性群組位於要從其移動的 Azure 區域中。

- Azure 網路安全性群組不能在區域之間移動。  您必須將新的 NSG 與目的地區域中的資源相關聯。

- 要匯出 NSG 配置並部署範本以在另一個區域中創建 NSG，您需要網路參與者角色或更高版本。

- 識別來源網路配置，以及您目前使用的所有資源。 此佈局包括但不限於負載等化器、公共 IP 和虛擬網路。

- 驗證 Azure 訂閱是否允許在使用的目的地區域中創建 NSG。 請連絡支援人員啟用所需的配額。

- 請確保您的訂閱有足夠的資源來支援此流程的 NSG 添加。  請參閱 [Azure 訂用帳戶和服務限制、配額與限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。


## <a name="prepare-and-move"></a>準備和移動
以下步驟演示如何使用資源管理器範本為配置和安全規則移動準備網路安全性群組，以及使用門戶將 NSG 配置和安全規則移動到目的地區域。


### <a name="export-the-template-and-deploy-from-the-portal"></a>匯出範本並從門戶進行部署

1. 登錄到[Azure 門戶](https://portal.azure.com) > **資源組**。
2. 找到包含源 NSG 的資源組並按一下它。
3. 選擇>**設置** > **匯出範本**。
4. 在 **"匯出範本"** 邊欄選項卡中選擇 **"部署**"。
5. 按一下**TEMPLATE** > **編輯參數**以在連線編輯器中打開**參數.json**檔。
6. 要編輯 NSG 名稱的參數，請更改**參數**下**的值**屬性：

    ```json
            {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
               "value": "<target-nsg-name>"
                }
               }
            }
    ```

7. 將編輯器中的源 NSG 值更改為目標 NSG 您選擇的名稱。 請確保將名稱括在引號中。

8.  按一下編輯器中的 **"保存**"。

9.  按一下**TEMPLATE** > **編輯範本**以在連線編輯器中打開**範本.json**檔。

10. 要編輯將移動 NSG 配置和安全規則的目的地區域，請更改連線編輯器中**資源**下**的位置**屬性：

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
           ]

    ```

11. 要獲取區域位置代碼，請參閱[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  區域的代碼是沒有空格的區功能變數名稱稱，**美國** = **中部中心**。

12. 如果願意，還可以更改範本中的其他參數，並且根據您的要求是可選的：

    * **安全規則**- 您可以通過向**範本.json**檔中**的安全規則**部分添加或刪除規則來編輯哪些規則部署到目標 NSG：

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
                    },
                ]
            }
        ```

      要完成目標 NSG 中規則的添加或刪除，還必須以下面的示例格式編輯**範本.json**檔末尾的自訂規則類型：

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

13. 按一下連線編輯器中的 **"保存**"。

14. 按一下**BASICS** > **訂閱**以選擇將部署目標 NSG 的訂閱。

15. 按一下**BASICS** > **資源組**以選擇將部署目標 NSG 的資源組。  可以按一下 **"創建新**"以創建目標 NSG 的新資源組。  確保名稱與現有 NSG 的源資源組不同。

16. 驗證**BASICS** > **位置**設置為您希望部署 NSG 的目標位置。

17. 在 **"設置"** 下驗證名稱是否與您在上述參數編輯器中輸入的名稱匹配。

18. 選中 **"條件"和"條件**"下的核取方塊。

19. 按一下 **"購買**"按鈕以部署目標網路安全性群組。

## <a name="discard"></a>捨棄

如果要放棄目標 NSG，請刪除包含目標 NSG 的資源組。  為此，請從門戶中的儀表板中選擇資源組，然後選擇概覽頁頂部的 **"刪除**"。

## <a name="clean-up"></a>清除

要提交更改並完成 NSG 的移動，請刪除源 NSG 或資源組。 為此，請從門戶中的儀表板中選擇網路安全性群組或資源組，然後在每個頁面頂部選擇 **"刪除**"。

## <a name="next-steps"></a>後續步驟

在本教程中，您將 Azure 網路安全性群組從一個區域移動到另一個區域並清理了源資源。  要瞭解有關在 Azure 中在區域和災害復原之間移動資源的詳細資訊，請參閱：


- [將資源移至新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [將 Azure VM 移至其他區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
