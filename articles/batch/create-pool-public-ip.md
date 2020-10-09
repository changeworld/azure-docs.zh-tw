---
title: 使用指定的公用 IP 位址建立集區
description: 瞭解如何建立使用您自己的公用 IP 位址的 Batch 集區。
ms.topic: how-to
ms.date: 10/08/2020
ms.openlocfilehash: e822311718847e173763847d503335f71457308b
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91849323"
---
# <a name="create-an-azure-batch-pool-with-specified-public-ip-addresses"></a>使用指定的公用 IP 位址建立 Azure Batch 集區

當您建立 Azure Batch 集區時，您可以 [在 Azure 虛擬網路的子網中](batch-virtual-network.md) 布建集區， (您指定的 VNet) 。 Batch 集區中的虛擬機器是透過 Batch 建立的公用 IP 位址來存取。 這些公用 IP 位址可能會在集區的存留期內變更，這表示如果未重新整理 IP 位址，您的網路設定可能會過期。

您可以建立靜態公用 IP 位址的清單，以與集區中的虛擬機器搭配使用。 這可讓您控制公用 IP 位址的清單，並確保不會意外變更。 如果您使用任何外部服務（例如資料庫）來限制存取特定的 IP 位址，這會特別有用。

如需建立沒有公用 IP 位址之集區的相關資訊，請參閱 [建立沒有公用 ip 位址的 Azure Batch 集](./batch-pool-no-public-ip-address.md)區。

## <a name="prerequisites"></a>Prerequisites

- **驗證**。 若要使用公用 IP 位址，Batch 用戶端 API 必須使用 [Azure Active Directory (AD) 驗證](batch-aad-auth.md)。

- **Azure VNet**。 您必須使用您要在其中建立集區和 IP 位址的相同 Azure 訂用帳戶中的 [虛擬網路](batch-virtual-network.md) 。 只能使用以 Azure Resource Manager 為基礎的 Vnet。 請確定 VNet 符合所有 [一般需求](batch-virtual-network.md#vnet-requirements)。

- **至少一個 Azure 公用 IP 位址**。 若要建立一或多個公用 IP 位址，您可以使用 [Azure 入口網站](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)、 [Azure Command-Line 介面 (CLI) ](/cli/azure/network/public-ip#az-network-public-ip-create)或 [Azure PowerShell](/powershell/module/az.network/new-azpublicipaddress)。 請務必遵循以下所列的需求。

> [!NOTE]
> Batch 會在資源群組中自動設定包含公用 IP 位址的其他網路資源。 針對每個100專用節點，Batch 通常會配置一個網路安全性群組 (NSG) 和一個負載平衡器。 這些資源會受到訂用帳戶資源配額的限制。 使用較大的集區時，您可能需要為一或多個這些資源 [要求增加配額](batch-quota-limit.md#increase-a-quota) 。

## <a name="public-ip-address-requirements"></a>公用 IP 位址需求

建立公用 IP 位址時，請記住下列需求：

- 公用 IP 位址必須與您用來建立集區的 Batch 帳戶位於相同的訂用帳戶和區域中。
- **IP 位址指派**必須設定為**靜態**。
- **SKU** 必須設定為 **標準**。
- 必須指定 DNS 名稱。
- 公用 IP 位址必須僅供虛擬機器設定集區使用。 其他資源都不應使用這些 IP 位址，否則集區可能會遇到配置失敗。
- 任何安全性原則或資源鎖定都不應限制使用者對公用 IP 位址的存取。
- 針對集區指定的公用 IP 位址數目必須夠大，才能容納以集區為目標的 Vm 數目。 這至少必須是集區的 **>createpool**   和 **>targetdedicatednodes**   屬性的總和。 如果沒有足夠的 IP 位址，集區會部分配置計算節點，而且會發生調整大小錯誤。 目前，Batch 會針對每個 100 Vm 使用一個公用 IP 位址。
- 一律具有公用 IP 位址的其他緩衝區。 建議您至少新增一個額外的公用 IP 位址，或新增至集區的公用 IP 位址總計的10% （以較大者為准）。 當相應減少時，這個額外的緩衝區將可協助 Batch 進行內部優化，也可讓您在升級失敗或縮小規模之後，更快速地進行擴充。
- 建立集區之後，您就無法新增或變更集區所使用的公用 IP 位址清單。 如果您需要修改清單，則必須刪除集區，然後再重新建立。

## <a name="create-a-batch-pool-with-public-ip-addresses"></a>使用公用 IP 位址建立 Batch 集區

下列範例顯示如何使用 [Azure Batch 服務 REST API](/rest/api/batchservice/pool/add) 來建立使用公用 IP 位址的集區。

### <a name="batch-service-rest-api"></a>Batch 服務 REST API

REST API URI

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

要求本文

```json
"pool": {
      "id": "pool2",
      "vmSize": "standard_a1",
      "virtualMachineConfiguration": {
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04.0-LTS"
        },
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
      },
"networkConfiguration": {
          "subnetId": "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/virtualNetworks/<vNetId>/subnets/<subnetId>",
          "publicIPAddressConfiguration": {
            "provision": "usermanaged",
            "ipAddressIds": [
              "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/publicIPAddresses/<publicIpId>"
          ]
        },

       "resizeTimeout":"PT15M",
      "targetDedicatedNodes":5,
      "targetLowPriorityNodes":0,
      "taskSlotsPerNode":3,
      "taskSchedulingPolicy": {
        "nodeFillType":"spread"
      },
      "enableAutoScale":false,
      "enableInterNodeCommunication":true,
      "metadata": [ {
        "name":"myproperty",
        "value":"myvalue"
      } ]
    }
```

## <a name="next-steps"></a>後續步驟

- 了解 [Batch 服務工作流程和主要資源](batch-service-workflow-features.md)，例如集區、節點、作業和工作。
- 瞭解如何 [在 Azure 虛擬網路的子網中建立集](batch-virtual-network.md)區。
- 瞭解如何 [建立沒有公用 IP 位址的 Azure Batch 集](./batch-pool-no-public-ip-address.md)區。

