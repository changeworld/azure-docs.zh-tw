---
title: 將 Azure 公用 IP 設定移至另一個 Azure 區域 Azure 入口網站
description: 使用範本，使用 Azure 入口網站將 Azure 公用 IP 設定從一個 Azure 區域移至另一個。
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 23fe515ddfdecb9ef168dd662e3fa2d91ece688f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84711471"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-the-azure-portal"></a>使用 Azure 入口網站將 Azure 公用 IP 設定移到另一個區域

有許多情況會讓您想要將現有的 Azure 公用 IP 設定從某個區域移至另一個。 例如，您可能會希望使用相同的設定和 SKU 來建立公用 IP 以進行測試。 基於災害復原規劃，您可能也會想要將公用 IP 設定移至另一個區域。

**Azure 公用 IP 特定於區域，且無法從某個區域移至另一個區域。** 不過，您可使用 Azure Resource Manager 範本來匯出公用 IP 的現有設定。  接著，您可將公用 IP 匯出至範本、修改參數以符合目的地區域，然後將範本部署到新的區域，藉此將資源放在另一個區域中。  如需有關 Resource Manager 和範本的詳細資訊，請參閱[快速入門：使用 Azure 入口網站建立及部署 Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。


## <a name="prerequisites"></a>必要條件

- 請確定 Azure 公用 IP 確實位在想要從其移動的 Azure 區域中。

- Azure 公用 Ip 無法在區域之間移動。  您必須將新公用 IP 與目標區域中的資源建立關聯。

- 若要匯出公用 IP 設定並部署範本以在另一個區域中建立公用 IP，則需要網路參與者角色或權限更高的角色。

- 識別來源網路配置，以及您目前使用的所有資源。 此配置包括 (但不限於) 負載平衡器、網路安全性群組 (NSG) 及虛擬網路。

- 請驗證 Azure 訂用帳戶允許在所使用目標區域中建立 VM。 請連絡支援人員啟用所需的配額。

- 請確認訂用帳戶具有足夠資源，能支援新增公用 IP 以用於此程序。  請參閱 [Azure 訂用帳戶和服務限制、配額與限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。


## <a name="prepare-and-move"></a>準備及移動
下列步驟示範如何使用 Resource Manager 範本來準備設定移動的公用 IP，並使用 Azure 入口網站將公用 IP 設定移至目的地區域。

### <a name="export-the-template-and-deploy-from-a-script"></a>匯出範本並從指令碼部署

1. 登入[Azure 入口網站](https://portal.azure.com)  >  **資源群組**。
2. 找出包含來源公用 IP 的資源群組，然後按一下它。
3. 選取 >**設定**] [  >  **匯出範本**]。
4. 在 [**匯出範本**] 分頁中，選擇 [**部署**]。
5. 按一下 [**範本**]  >  [**編輯參數**]，在線上編輯器中開啟檔案的**parameters.js** 。
8. 若要編輯公用 IP 名稱的參數，請將 [**參數**值] 底下的屬性  >  **value**從來源 [公用 ip 名稱] 變更為目標公用 ip 的名稱，確定名稱是以引號括住：

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
8.  按一下編輯器中的 [**儲存**]。

9.  按一下 [**範本**]  >  [**編輯範本**]，在線上編輯器中開啟檔案上的**template.js** 。

10. 若要編輯將移動公用 IP 的目的地區域，請變更 [**資源**] 底下的 [**位置**] 屬性：

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

11. 若要取得區域位置代碼，請參閱[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  區域的程式碼是不含空格、**美國中部**  =  **centralus**的區功能變數名稱稱。

12. 您也可以根據需求而選擇變更範本中的其他參數：

    * **Sku** -您可以變更template.json 檔案中的**sku**  >  **名稱**屬性，將設定中的公用 IP sku 從標準變更為基本或基本到標準**template.json** ：

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

        如需基本和標準 sku 公用 ip 之間差異的詳細資訊，請參閱[建立、變更或刪除公用 IP 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)：

    * **公用 IP 配置方法**和**閒置逾時** - 您可將 **publicIPAllocationMethod** 屬性從**動態**變更為**靜態**，或從**靜態**變更為**動態**，以在範本中變更這兩個選項。 將 **idleTimeoutInMinutes** 屬性變更為所要的數量，即可變更閒置時間。  預設值為 **4**：

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

        如需配置方法和閒置逾時值的詳細資訊，請參閱[建立、變更或刪除公用 IP 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address) (機器翻譯)。


13. 按一下線上編輯器中的 [**儲存**]。

14. 按一下 [**基本**] [訂用帳戶]  >  **Subscription** ，選擇將部署目標公用 IP 的訂用帳戶。

15. 按一下 [**基本**]  >  [**資源群組**]，選擇將部署目標公用 IP 的資源群組。  您可以按一下 [**建立新**的]，為目標公用 IP 建立新的資源群組。  請確定名稱與現有來源公用 IP 的來源資源群組不同。

16. 確認 [**基本**]  >  [**位置**] 已設定為您想要部署公用 IP 的目標位置。

17. 確認 [**設定**] 底下的 [名稱] 符合您在上面的 [參數編輯器] 中輸入的名稱。

18. 勾選 [**條款及條件**] 底下的方塊。

19. 按一下 [**購買**] 按鈕以部署目標公用 IP。

## <a name="discard"></a>捨棄

如果您想要捨棄目標公用 IP，請刪除包含目標公用 IP 的資源群組。  若要這麼做，請從入口網站的儀表板中選取資源群組，然後選取 [總覽] 頁面頂端的 [**刪除**]。

## <a name="clean-up"></a>清除

若要認可變更並完成公用 IP 的移動，請刪除來源公用 IP 或資源群組。 若要這麼做，請從入口網站的儀表板中選取公用 IP 或資源群組，然後選取每個頁面頂端的 [**刪除**]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將 Azure 公用 IP 從某區域移至另一個區域，並清除了來源資源。  若要深入了解如何在 Azure 中的區域之間移動資源和災害復原，請參閱：


- [將資源移至新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [將 Azure VM 移至其他區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
