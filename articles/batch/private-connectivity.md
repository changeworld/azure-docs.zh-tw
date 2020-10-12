---
title: 使用私人端點搭配 Azure Batch 帳戶
description: 瞭解如何使用私人端點私下連接到 Azure Batch 帳戶。
ms.topic: how-to
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: 7dba3dd1d34421666821c6bc7320ef76ab77bb7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542133"
---
# <a name="use-private-endpoints-with-azure-batch-accounts"></a>使用私人端點搭配 Azure Batch 帳戶

根據預設， [Azure Batch 帳戶](accounts.md) 具有公用端點，而且可公開存取。 Batch 服務提供建立私人 Batch 帳戶的能力，並停用公用網路存取。

藉由使用 [Azure Private Link](../private-link/private-link-overview.md)，您可以透過 [私人端點](../private-link/private-endpoint-overview.md)連接至 Azure Batch 帳戶。 私人端點是虛擬網路內子網路中的一組私人 IP 位址。 然後，您可以透過私人 IP 位址限制對 Azure Batch 帳戶的存取。

Private Link 可讓使用者從虛擬網路或任何對等互連的虛擬網路中存取 Azure Batch 帳戶。 您也可以透過 VPN 或 [Azure ExpressRoute](../expressroute/expressroute-introduction.md)，透過私人對等互連，在內部部署中存取對應至 Private Link 的資源。 您可以使用 [自動或手動核准方法](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow)，連接到以 Private Link 設定的 Azure Batch 帳戶。

> [!IMPORTANT]
> 在 Azure Batch 中的私人連線支援目前適用于所有公用區域，但德國中部和德國東北部除外。

本文說明使用私人端點建立私人 Batch 帳戶並加以存取的步驟。

## <a name="azure-portal"></a>Azure 入口網站

使用下列步驟，使用 Azure 入口網站建立私人 Batch 帳戶：

1. 從 [ **建立資源** ] 窗格中，選擇 [ **Batch 服務** ]，然後選取 [ **建立**]。
2. 在 [ **基本** ] 索引標籤中輸入訂用帳戶、資源群組、區域和 Batch 帳戶名稱，然後選取 **[下一步： Advanced**]。
3. 在 [ **Advanced** ] 索引標籤中，將 [**公用網路存取**] 設定為 [**停用**
4. 在 [ **設定**] 中，選取 [ **私人端點連接** ]，然後選取 [ **+ 私人端點**]。
   :::image type="content" source="media/private-connectivity/private-endpoint-connections.png" alt-text="私人端點連接":::
5. 在 [ **基本** ] 窗格中，輸入或選取訂用帳戶、資源群組、私人端點資源名稱和區域詳細資料，然後選取 **[下一步：資源]**。
6. 在 [ **資源** ] 窗格中，將 **資源類型** 設定為 **Microsoft.Batch/batchAccounts**。 選取您要存取的私人 Batch 帳戶，然後選取 **[下一步：設定]**。
   :::image type="content" source="media/private-connectivity/create-private-endpoint.png" alt-text="私人端點連接":::
7. **在 [設定**] 窗格中，輸入或選取這項資訊：
   - **虛擬網路**：選取您的虛擬網路。
   - **子網**：選取您的子網。
   - **與私人 DNS 區域整合**：選取 **[是]**。 若要私下與私人端點連線，則需要 DNS 記錄。 我們建議將私人端點與私人 DNS 區域整合。 您也可以使用自己的 DNS 伺服器，或藉由使用虛擬機器上的主機檔案來建立 DNS 記錄。
   - **私人 DNS 區域**：選取 [privatelink <region> ]。batch.azure.com。 系統會自動決定私人 DNS 區域。 您無法使用 Azure 入口網站來加以變更。
8. 選取 [ **審核] + [建立**]，然後等待 Azure 驗證您的設定。
9. 當您看到 [驗證成功] 訊息時，請選取 [建立]。

布建私人端點之後，您可以使用私人端點，從相同虛擬網路中的 Vm 存取 Batch 帳戶。 若要從 Azure 入口網站中查看 IP 位址：

1. 選取 [所有資源]。
2. 搜尋稍早建立的私人端點。
3. 選取 [概觀] 索引標籤，以查看 DNS 設定和 IP 位址。

:::image type="content" source="media/private-connectivity/access-private.png" alt-text="私人端點連接":::

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本

[使用 Azure Resource Manager 範本建立 Batch 帳戶](quick-create-template.md)時，請修改範本，將**將 publicnetworkaccess 新增**設定為**停用**，如下所示。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "batchAccountName": {
      "type": "string",
      },
    "location": {
      "type": "string",
    }
  },
  "resources": [
    {
      "name": "[parameters('batchAccountName')]",
      "type": "Microsoft.Batch/batchAccounts",
      "apiVersion": "2020-03-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": []
      "properties": {
        "poolAllocationMode": "BatchService"
        "publicNetworkAccess": "Disabled"
      }
    }
  ]
}
```

## <a name="configure-dns-zones"></a>設定 DNS 區域

使用您已在其中建立私人端點的子網內的 [私人 DNS 區域](../dns/private-dns-privatednszone.md) 。 設定端點，讓每個私人 IP 位址對應至 DNS 項目

當您建立私人端點時，您可以將它與 Azure 中的 [私人 DNS 區域](../dns/private-dns-privatednszone.md) 整合。 如果您選擇改為使用 [自訂網域](../dns/dns-custom-domain.md)，您必須將它設定為針對私人端點保留的所有私人 IP 位址新增 DNS 記錄。

## <a name="pricing"></a>定價

如需私人端點相關成本的詳細資訊，請參閱 [Azure Private Link 定價](https://azure.microsoft.com/pricing/details/private-link/)。

## <a name="current-limitations-and-best-practices"></a>目前的限制和最佳作法

建立私人 Batch 帳戶時，請記住下列事項：

- 私人端點資源必須建立在與 Batch 帳戶相同的訂用帳戶中。
- 若要刪除私人連接，您必須刪除私人端點資源。
- 使用公用網路存取建立 Batch 帳戶之後，您就無法將它變更為僅限私人存取。
- 當您刪除私人端點或從 Batch 帳戶移除區域時，不會自動移除私人 DNS 區域中的 DNS 記錄。 您必須手動移除 DNS 記錄，才能新增連結至此私人 DNS 區域的新私人端點。 如果您未清除 DNS 記錄，可能會發生未預期的資料平面問題，例如，在移除私人端點或移除區域之後新增的區域資料中斷。

## <a name="next-steps"></a>後續步驟

- 瞭解如何 [在虛擬網路中建立 Batch](batch-virtual-network.md)集區。
- 瞭解如何[建立沒有公用 IP 位址的 Batch](batch-pool-no-public-ip-address.md)集區
- 瞭解如何 [使用指定的公用 IP 位址建立 Batch](create-pool-public-ip.md)集區。
- 瞭解 [Azure Private Link](../private-link/private-link-overview.md)。
