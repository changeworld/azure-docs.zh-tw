---
title: 使用 Azure 入口網站將 Azure 網路安全性群組 (NSG) 移至另一個 Azure 區域
description: 使用 Azure Resource Manager 範本，以使用 Azure 入口網站將 Azure 網路安全性群組從一個 Azure 區域移至另一個區域。
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: a22dc6dc0c4fc199d3f262b18aeeae5090a06dce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84689311"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>使用 Azure 入口網站將 Azure 網路安全性群組 (NSG) 移至另一個區域

在許多情況下，您會想要將現有的 Nsg 從一個區域移到另一個區域。 例如，您可能會想要使用相同的設定和安全性規則來建立 NSG 來進行測試。 您也可能想要將 NSG 移至另一個區域，作為損毀修復計畫的一部分。

Azure 安全性群組無法從一個區域移至另一個區域。 不過，您可以使用 Azure Resource Manager 範本來匯出 NSG 的現有設定和安全性規則。  然後，您可以將 NSG 匯出至範本、修改參數以符合目的地區域，然後將範本部署到新的區域，以暫存另一個區域中的資源。  如需有關 Resource Manager 和範本的詳細資訊，請參閱[快速入門：使用 Azure 入口網站建立及部署 Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。


## <a name="prerequisites"></a>Prerequisites

- 請確定 Azure 網路安全性群組位於您要移動的 Azure 區域中。

- 無法在區域之間移動 Azure 網路安全性群組。  您必須將新的 NSG 與目的地區域中的資源產生關聯。

- 若要匯出 NSG 設定並部署範本，以在另一個區域中建立 NSG，您需要「網路參與者」角色或更高的角色。

- 識別來源網路配置，以及您目前使用的所有資源。 此版面配置包含但不限於負載平衡器、公用 Ip 及虛擬網路。

- 確認您的 Azure 訂用帳戶可讓您在使用的目的地區域中建立 Nsg。 請連絡支援人員啟用所需的配額。

- 請確定您的訂用帳戶有足夠的資源，可支援在此程式中加入 Nsg。  請參閱 [Azure 訂用帳戶和服務限制、配額與限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。


## <a name="prepare-and-move"></a>準備及移動
下列步驟說明如何使用 Resource Manager 範本準備設定和安全性規則移動的網路安全性群組，並使用入口網站將 NSG 設定和安全性規則移至目的地區域。


### <a name="export-the-template-and-deploy-from-the-portal"></a>從入口網站匯出範本和部署

1. 登入[Azure 入口網站](https://portal.azure.com)  >  **資源群組**。
2. 找出包含來源 NSG 的資源群組，然後按一下它。
3. 選取 >**設定**  >  **匯出範本**。
4. 選擇 [**匯出範本**] 分頁中的 [**部署**]。
5. 按一下 [**範本**  >  **編輯參數**]，在線上編輯器中開啟檔案**parameters.js** 。
6. 若要編輯 NSG 名稱的參數，請變更 [**參數**] 下的 [**值**] 屬性：

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

7. 將編輯器中的 [來源 NSG] 值變更為您選擇的目標 NSG 名稱。 請確定以引號括住名稱。

8.  在編輯器中按一下 [ **儲存** ]。

9.  按一下 [**範本**  >  **編輯範本**]，在線上編輯器中開啟檔案**template.js** 。

10. 若要編輯 NSG 設定和安全性規則將移至其中的目的地區域，請在 [線上編輯器] 的 [**資源**] 下變更 [**位置**] 屬性：

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

11. 若要取得區域位置代碼，請參閱 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  區域的程式碼是區功能變數名稱稱，不含空格、**美國中部**  =  **centralus**。

12. 您也可以根據需求而選擇變更範本中的其他參數：

    * **安全性規則**-您可以在**template.jsOn**檔案的**securityRules**區段中新增或移除規則，以編輯要部署到目標 NSG 的規則：

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

      若要完成新增或移除目標 NSG 中的規則，您也必須以下列範例的格式，在檔案 **template.js** 結尾編輯自訂規則類型：

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

13. 在線上編輯器中按一下 [ **儲存** ]。

14. 按一下 [**基本**]  >  **訂**用帳戶，選擇將部署目標 NSG 的訂用帳戶。

15. 按一下 [**基本**  >  **資源群組**]，選擇將部署目標 NSG 的資源群組。  您可以按一下 [ **建立新** 的]，為目標 NSG 建立新的資源群組。  請確定名稱與現有 NSG 的來源資源群組不相同。

16. 確認**基本**  >  **位置**已設為您想要部署 NSG 的目標位置。

17. 在 [ **設定** ] 底下，確認名稱符合您在上述 [參數編輯器] 中輸入的名稱。

18. 勾選 [ **條款及條件**] 底下的方塊。

19. 按一下 [ **購買** ] 按鈕以部署目標網路安全性群組。

## <a name="discard"></a>捨棄

如果您想要捨棄目標 NSG，請刪除包含目標 NSG 的資源群組。  若要這樣做，請從入口網站中的儀表板選取資源群組，然後選取 [總覽] 頁面頂端的 [ **刪除** ]。

## <a name="clean-up"></a>清除

若要認可變更並完成 NSG 的移動，請刪除來源 NSG 或資源群組。 若要這樣做，請從入口網站中的儀表板選取網路安全性群組或資源群組，然後選取每個頁面頂端的 [ **刪除** ]。

## <a name="next-steps"></a>接下來的步驟

在本教學課程中，您已將 Azure 網路安全性群組從一個區域移至另一個區域，並清除了來源資源。  若要深入了解如何在 Azure 中的區域之間移動資源和災害復原，請參閱：


- [將資源移至新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [將 Azure VM 移至其他區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
