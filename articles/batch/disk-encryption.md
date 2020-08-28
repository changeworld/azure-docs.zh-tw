---
title: 建立已啟用磁片加密的集區
description: 瞭解如何使用磁片加密設定，以平臺管理的金鑰來加密節點。
author: pkshultz
ms.topic: how-to
ms.date: 08/25/2020
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: 5210ead0a85cff27c38d9ff9fb0d387e3799428c
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89038479"
---
# <a name="create-a-pool-with-disk-encryption-enabled"></a>建立已啟用磁片加密的集區

當您使用虛擬機器設定建立 Azure Batch 集區時，您可以藉由指定磁片加密設定，以平臺管理的金鑰來加密集區中的計算節點。 

本文說明如何建立已啟用磁片加密的 Batch 集區。 

## <a name="why-use-a-pool-with-disk-encryption-configuration"></a>為何要使用具有磁片加密設定的集區？

有了 Batch 集區，您就可以存取和儲存計算節點的作業系統和暫存磁片上的資料。 使用平臺管理的金鑰來加密伺服器端磁片，可保護此資料的額外負荷和便利性。  

Batch 會根據集區設定和區域支援，在計算節點上套用這些磁片加密技術的其中一種。 

* [使用平臺管理的金鑰進行靜態受控磁片加密](../virtual-machines/windows/disk-encryption.md#platform-managed-keys) 

* [使用平臺管理的金鑰在主機上加密](../virtual-machines/windows/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data) 

* [Azure 磁碟加密](../security/fundamentals/azure-disk-encryption-vms-vmss.md) 

> [!IMPORTANT]
> 在 Azure Batch 中使用平臺管理金鑰的主機加密支援目前為美國東部、美國西部2、美國中南部、US Gov 維吉尼亞州和 US Gov 亞利桑那州區域提供公開預覽。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您將無法指定要將哪些加密方法套用至集區中的節點。 相反地，您會提供您想要在其節點上加密的目標磁片，而 Batch 可以選擇適當的加密方法，以確保在計算節點上加密指定的磁片。
 
## <a name="azure-portal"></a>Azure 入口網站 

在 Azure 入口網站中建立 Batch 集區時，請選取 [**磁片加密**設定] 下的 [ **TemporaryDisk** ] 或 [ **OsAndTemporaryDisk** ]。

![Azure 入口網站中 [磁片加密] 設定選項的螢幕擷取畫面。](./media/disk-encryption/portal-view.png)

建立集區之後，您可以在集區的 [ **屬性** ] 區段中看到磁片加密設定目標。

![顯示 Azure 入口網站中磁片加密設定目標的螢幕擷取畫面。](./media/disk-encryption/disk-encryption-configuration-target.png)

## <a name="examples"></a>範例

下列範例示範如何使用 Batch .NET SDK、Batch REST API 和 Azure CLI 來加密 Batch 集區上的 OS 和暫存磁片。

### <a name="batch-net-sdk"></a>Batch .NET SDK 

```csharp
pool.VirtualMachineConfiguration.DiskEncryptionConfiguration = new DiskEncryptionConfiguration(
    targets: new List<DiskEncryptionTarget> { DiskEncryptionTarget.OsDisk, DiskEncryptionTarget.TemporaryDisk }
    );
```

### <a name="batch-rest-api"></a>Batch REST API


REST API URL：
```
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```
要求本文：
```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "18.04-LTS"
        },
        "diskEncryptionConfiguration": {
            "targets": [
                "OsDisk",
                "TemporaryDisk"
            ]
        }
        "nodeAgentSKUId": "batch.node.ubuntu 18.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "maxTasksPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az batch pool create \
    --id diskencryptionPool \
    --vm-size Standard_DS1_V2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:18.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 18.04" \
    --disk-encryption-targets OsDisk TemporaryDisk
```
