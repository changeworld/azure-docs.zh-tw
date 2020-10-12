---
title: 建立或更新作業的 PUT 呼叫
description: 在計算資源上放置建立或更新作業的呼叫
author: mimckitt
ms.author: mimckitt
ms.reviewer: cynthn
ms.topic: conceptual
ms.service: virtual-machines
ms.date: 08/4/2020
ms.custom: avverma
ms.openlocfilehash: c57025346a9f623e3fe5536b36820ea62f355cc0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91342685"
---
# <a name="put-calls-for-creation-or-updates-on-compute-resources"></a>在計算資源上放置呼叫以進行建立或更新

`Microsoft.Compute` 資源不支援 *HTTP PUT* 語義的傳統定義。 相反地，這些資源會針對 PUT 和 PATCH 動詞命令使用 PATCH 語義。

**建立** 作業會在適當時套用預設值。 不過，透過 PUT 或 PATCH 完成的資源 **更新** ，不會套用任何預設屬性。 **更新** 作業適用套用嚴格修補程式的語法。

例如， `caching` `ReadWrite` 如果資源是 OS 磁片，則虛擬機器的 disk 屬性預設為。

```json
    "storageProfile": {
      "osDisk": {
        "name": "myVMosdisk",
        "image": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/{existing-generalized-os-image-blob-name}.vhd"
        },
        "osType": "Windows",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "vhd": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/myDisk.vhd"
        }
      }
    },
```

不過，當屬性已離開或傳遞*null*值時，對於**更新**作業，它會保持不變，而且沒有預設值。

將更新作業傳送至資源時，這一點很重要，因為這是移除關聯的目的。 如果該資源是 `Microsoft.Compute` 資源，您要移除的對應屬性必須明確地呼叫並指派值。 為了達成此目的，使用者可以傳遞空字串，例如 **""**。 這會指示平臺移除該關聯。

> [!IMPORTANT]
> 不支援「修補」陣列元素。 相反地，用戶端必須使用更新陣列的完整內容來進行 PUT 或 PATCH 要求。 例如，若要從 VM 卸離資料磁片，請進行 GET 要求以取得目前的 VM 模型，移除要中斷連結的磁片， `properties.storageProfile.dataDisks` 並使用更新的 VM 實體進行 PUT 要求。

## <a name="examples"></a>範例

### <a name="correct-payload-to-remove-a-proximity-placement-groups-association"></a>更正承載以移除鄰近放置群組關聯

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": "" } }
`

### <a name="incorrect-payloads-to-remove-a-proximity-placement-groups-association"></a>不正確的裝載以移除鄰近放置群組關聯

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": null } }
`

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20 } }
`

## <a name="next-steps"></a>後續步驟
深入瞭解如何建立或更新[虛擬機器](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate)和[虛擬機器擴展集](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)的呼叫

