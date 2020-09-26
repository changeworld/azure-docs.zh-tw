---
title: 建立不具公用 IP 位址的 Azure Batch 集區
description: 瞭解如何建立不具公用 IP 位址的集區
author: pkshultz
ms.topic: how-to
ms.date: 09/25/2020
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: 9b36c769c70792e47464c2704e1912dbb2d744dd
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91367932"
---
# <a name="create-an-azure-batch-pool-without-public-ip-addresses"></a>建立不具公用 IP 位址的 Azure Batch 集區

當您建立 Azure Batch 集區時，您可以在不使用公用 IP 位址的情況下布建虛擬機器設定集區。 本文說明如何設定不含公用 IP 位址的 Batch 集區。

## <a name="why-use-a-pool-without-public-ip-addresses"></a>為何要使用沒有公用 IP 位址的集區？

根據預設，Azure Batch 虛擬機器設定集區中的所有計算節點都會被指派一個公用 IP 位址。 Batch 服務會使用此位址來排程工作，以及與計算節點進行通訊，包括對網際網路的輸出存取。

若要限制這些節點的存取權，並減少這些節點在網際網路上的可探索性，您可以布建沒有公用 IP 位址的集區。

> [!IMPORTANT]
> Azure Batch 中不含公用 IP 位址的集區支援目前在所有區域均為公開預覽狀態，但中國東部、中國東部2、中國北部和中國北部2除外。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

- **驗證**。 若要在 [虛擬網路](./batch-virtual-network.md)內使用不具公用 IP 位址的集區，Batch 用戶端 API 必須使用 AZURE ACTIVE DIRECTORY (AD) 驗證。 Azure Batch 對於 Azure AD 的支援記載於[使用 Active Directory 驗證 Batch 服務解決方案](batch-aad-auth.md)中。 如果您不是在虛擬網路內建立集區，則可以使用 Azure AD authentication 或金鑰型驗證。

- **Azure VNet**。 如果您是在 [虛擬網路](batch-virtual-network.md)中建立集區，請遵循這些需求和設定。 若要事先準備具有一個或多個子網路的 VNet，您可以使用 Azure 入口網站、Azure PowerShell、Azure 命令列介面 (CLI) 或其他方法。
  - VNet 必須與您用來建立集區的 Batch 帳戶位於相同的訂用帳戶和區域中。
  - 針對集區指定的子網路必須有足夠的未指派 IP 位址，可容納目標設為集區的 VM 數目；也就是集區之 `targetDedicatedNodes` 和 `targetLowPriorityNodes` 屬性的總和。 如果子網路沒有足夠的未指派 IP 位址，集區會局部配置計算節點，並發生調整大小錯誤。
  - 您必須停用 private link 服務和端點網路原則。 您可以使用 Azure CLI 來完成這項操作： ```az network vnet subnet update --vnet-name <vnetname> -n <subnetname> --disable-private-endpoint-network-policies --disable-private-link-service-network-policies```
  
> [!IMPORTANT]
> 針對每個100專用或低優先順序節點，Batch 會配置一個私用連結服務和一個負載平衡器。 這些資源會被訂用帳戶的[資源配額](../azure-resource-manager/management/azure-subscription-service-limits.md)所限制。 針對大型集區，您可能需要為一或多個這些資源 [要求增加配額](batch-quota-limit.md#increase-a-quota) 。 此外，您不應該將資源鎖定套用至 Batch 所建立的任何資源，因為這可避免因為使用者起始的動作（例如刪除集區或調整為零）而造成資源清除。

## <a name="current-limitations"></a>目前的限制

1. 沒有公用 IP 位址的集區必須使用虛擬機器設定，而不是雲端服務設定。
1. Batch 計算節點的[自訂端點](pool-endpoint-configuration.md)設定無法使用沒有公用 IP 位址的集區。
1. 因為沒有公用 IP 位址，所以您無法在此類型的集區中 [使用您自己指定的公用 ip 位址](create-pool-public-ip.md) 。

## <a name="create-a-pool-without-public-ip-addresses-in-the-azure-portal"></a>在 Azure 入口網站中建立不具公用 IP 位址的集區

1. 在 Azure 入口網站中瀏覽至您的 Batch 帳戶。 
1. 在左側的 [ **設定** ] 視窗中， **選取 [** 集區]。
1. **在 [集**區] 視窗中，選取 [**新增**]。
1. 在 [新增集區] 視窗上，從 [映像類型] 下拉式清單選取您要使用的選項。
1. 選取您映射的正確 **發行者/供應專案/Sku** 。
1. 指定其餘的必要設定，包括 **節點大小**、 **目標專用節點**和 **低優先順序節點**，以及任何所需的選擇性設定。
1. 選擇性地選取您想要使用的虛擬網路和子網。 此虛擬網路必須與您建立的集區位於相同的資源群組中。
1. 在 [ **IP 位址**布建類型] 中，選取 [ **NoPublicIPAddresses**]。

![[新增集區] 畫面的螢幕擷取畫面，其中已選取 [NoPublicIPAddresses]。](./media/batch-pool-no-public-ip-address/create-pool-without-public-ip-address.png)

## <a name="use-the-batch-rest-api-to-create-a-pool-without-public-ip-addresses"></a>使用 Batch REST API 建立不具公用 IP 位址的集區

下列範例顯示如何使用 [Azure Batch REST API](/rest/api/batchservice/pool/add) 來建立使用公用 IP 位址的集區。

### <a name="rest-api-uri"></a>REST API URI

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

### <a name="request-body"></a>Request body

```json
"pool": {
     "id": "pool2",
     "vmSize": "standard_a1",
     "virtualMachineConfiguration": {
          "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.040-LTS"
          },
     "nodeAgentSKUId": "batch.node.ubuntu 16.04"
     }
     "networkConfiguration": {
          "subnetId": "/subscriptions/<your_subscription_id>/resourceGroups/<your_resource_group>/providers/Microsoft.Network/virtualNetworks/<your_vnet_name>/subnets/<your_subnet_name>",
          "publicIPAddressConfiguration": {
               "provision": "NoPublicIPAddresses"
          }
     },
     "resizeTimeout": "PT15M",
     "targetDedicatedNodes": 5,
     "targetLowPriorityNodes": 0,
     "maxTasksPerNode": 3,
     "taskSchedulingPolicy": {
          "nodeFillType": "spread"
     },
     "enableAutoScale": false,
     "enableInterNodeCommunication": true,
     "metadata": [
    {
      "name": "myproperty",
      "value": "myvalue"
    }
       ]
}
```

## <a name="outbound-access-to-the-internet"></a>網際網路的輸出存取

在沒有公用 IP 位址的集區中，除非您適當地設定網路設定，例如使用 [虛擬網路 NAT](../virtual-network/nat-overview.md)，否則您的虛擬機器將無法存取公用網際網路。 請注意，NAT 只允許從虛擬網路中的虛擬機器對網際網路進行輸出存取。 因為批次建立的計算節點沒有相關聯的公用 IP 位址，所以無法公開存取。

提供輸出連線能力的另一種方式是使用使用者定義的路由 (UDR) 。 這可讓您將流量路由傳送至具有公用網際網路存取的 proxy 電腦。

## <a name="next-steps"></a>後續步驟

- 深入瞭解如何 [在虛擬網路中建立](batch-virtual-network.md)集區。
- 瞭解如何搭配 [Batch 帳戶使用私人端點](private-connectivity.md)。
