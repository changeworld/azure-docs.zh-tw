---
title: 使用 Azure 門戶將 Azure 公共 IP 移動到其他 Azure 區域
description: 使用 Azure 資源管理器範本使用 Azure 門戶將 Azure 公共 IP 從一個 Azure 區域移動到另一個 Azure 區域。
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 6d10265e8383b68ebe13c95d8b2a9632668e85da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641391"
---
# <a name="move-azure-public-ip-to-another-region-using-the-azure-portal"></a>使用 Azure 門戶將 Azure 公共 IP 移動到其他區域

在各種方案中，您希望將現有的 Azure 公共 IP 從一個區域移動到另一個區域。 例如，您可能希望創建具有相同配置和 sKU 以進行測試的公共 IP。 作為災害復原規劃的一部分，您可能還希望將公共 IP 移動到其他區域。

Azure 公共 IP 特定于區域，不能從一個區域移動到另一個區域。 但是，可以使用 Azure 資源管理器範本匯出公共 IP 的現有配置。  然後，可以通過將公共 IP 匯出到範本、修改參數以匹配目的地區域，然後將範本部署到新區域，在另一個區域中暫按資源。  有關資源管理器和範本的詳細資訊，請參閱[快速入門：使用 Azure 門戶創建和部署 Azure 資源管理器範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。


## <a name="prerequisites"></a>Prerequisites

- 確保 Azure 公共 IP 位於要從其移動的 Azure 區域中。

- Azure 公共 IP 不能在區域之間移動。  您必須將新的公共 IP 與目的地區域中的資源相關聯。

- 要匯出公共 IP 配置並部署範本以在另一個區域創建公共 IP，您需要網路參與者角色或更高版本。

- 識別來源網路配置，以及您目前使用的所有資源。 此佈局包括但不限於負載等化器、網路安全性群組 （NSG） 和虛擬網路。

- 驗證 Azure 訂閱是否允許您在使用的目的地區域中創建公共 IP。 請連絡支援人員啟用所需的配額。

- 請確保您的訂閱有足夠的資源來支援為此過程添加公共 IP。  請參閱 [Azure 訂用帳戶和服務限制、配額與限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。


## <a name="prepare-and-move"></a>準備和移動
以下步驟演示如何使用資源管理器範本為配置移動準備公共 IP，以及使用 Azure 門戶將公共 IP 配置移動到目的地區域。

### <a name="export-the-template-and-deploy-from-a-script"></a>匯出範本並從腳本中部署

1. 登錄到[Azure 門戶](https://portal.azure.com) > **資源組**。
2. 找到包含源公共 IP 的資源組並按一下它。
3. 選擇>**設置** > **匯出範本**。
4. 在 **"匯出範本"** 邊欄選項卡中選擇 **"部署**"。
5. 按一下**TEMPLATE** > **編輯參數**以在連線編輯器中打開**參數.json**檔。
8. 要編輯公共 IP 名稱的參數，請在**參數** > **值**下的屬性從源公共 IP 名稱更改為目標公共 IP 的名稱，以確保名稱以引號形式：

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```
8.  按一下編輯器中的 **"保存**"。

9.  按一下**TEMPLATE** > **編輯範本**以在連線編輯器中打開**範本.json**檔。

10. 要編輯將移動公共 IP 的目的地區域，請更改**資源**下**的位置**屬性：

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

11. 要獲取區域位置代碼，請參閱[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  區域的代碼是沒有空格的區功能變數名稱稱，**美國** = **中部中心**。

12. 如果願意，還可以更改範本中的其他參數，並且根據您的要求是可選的：

    * **SKU** - 您可以通過更改**範本.json**檔中的**sku** > **名稱**屬性來更改配置中公共 IP 的 sKU 從標準更改為基本或基本到標準：

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

        有關基本和標準 sku 公共 ip 之間的差異的詳細資訊，請參閱[創建、更改或刪除公共 IP 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)：

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

        ```

        有關分配方法和空閒超時值的詳細資訊，請參閱[創建、更改或刪除公共 IP 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。


13. 按一下連線編輯器中的 **"保存**"。

14. 按一下**BASICS** > **訂閱**以選擇將部署目標公共 IP 的訂閱。

15. 按一下**BASICS** > **資源組**以選擇將部署目標公共 IP 的資源組。  可以按一下 **"創建新**"以創建目標公共 IP 的新資源組。  確保名稱與現有源公共 IP 的源資源組不同。

16. 驗證**BASICS** > **位置**設置為您希望部署公共 IP 的目標位置。

17. 在 **"設置"** 下驗證名稱是否與您在上述參數編輯器中輸入的名稱匹配。

18. 選中 **"條件"和"條件**"下的核取方塊。

19. 按一下 **"購買**"按鈕以部署目標公共 IP。

## <a name="discard"></a>捨棄

如果要放棄目標公共 IP，請刪除包含目標公共 IP 的資源組。  為此，請從門戶中的儀表板中選擇資源組，然後選擇概覽頁頂部的 **"刪除**"。

## <a name="clean-up"></a>清除

要提交更改並完成公共 IP 的移動，請刪除源公共 IP 或資源組。 為此，請從門戶中的儀表板中選擇公共 IP 或資源組，然後在每個頁面頂部選擇 **"刪除**"。

## <a name="next-steps"></a>後續步驟

在本教程中，您將 Azure 公共 IP 從一個區域移動到另一個區域並清理了源資源。  要瞭解有關在 Azure 中在區域和災害復原之間移動資源的詳細資訊，請參閱：


- [將資源移至新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [將 Azure VM 移至其他區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
