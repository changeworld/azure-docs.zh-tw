---
title: 適用于 Windows 的 Azure Instance Metadata Service
description: 瞭解 Azure Instance Metadata Service，以及它如何提供 Windows 中目前執行中虛擬機器實例的相關資訊。
services: virtual-machines
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/30/2020
ms.author: sukumari
ms.reviewer: azmetadatadev
ms.openlocfilehash: cc0d47807101a3cfbb26e7ea69cc7d117d3f9b31
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435224"
---
# <a name="azure-instance-metadata-service"></a>Azure 執行個體中繼資料服務

Azure Instance Metadata Service (IMDS) 提供目前執行中虛擬機器實例的相關資訊。 您可以使用它來管理和設定您的虛擬機器。
此資訊包括 SKU、儲存體、網路設定和即將進行的維護事件。 如需可用資料的完整清單，請參閱 [中繼資料 api](#metadata-apis)。


IMDS 可用於執行 (Vm) 和虛擬機器擴展集實例的虛擬機器實例。 所有 Api 都支援使用 [Azure Resource Manager](/rest/api/resources/)建立和管理的 vm。 只有證明和網路端點支援使用傳統部署模型建立的 Vm。 證明端點只會在有限範圍內執行。

IMDS 是可在已知且無法路由傳送的 IP 位址 () 的 REST 端點 `169.254.169.254` 。 您只能從 VM 中存取它。 VM 與 IMDS 之間的通訊絕不會離開主機。
當查詢 IMDS 時，您的 HTTP 用戶端會略過 VM 內的 web proxy，並且 `169.254.169.254` 將與相同 [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md) 。

## <a name="security"></a>安全性

只有在無法路由傳送的 IP 位址上執行中的虛擬機器實例內才能存取 IMDS 端點。 此外， `X-Forwarded-For` 服務會拒絕任何具有標頭的要求。
要求也必須包含 `Metadata: true` 標頭，以確保實際要求是直接預定的，而不是不慎重新導向的一部分。

> [!IMPORTANT]
> IMDS 不是敏感性資料的通道。 端點會開放給 VM 上的所有進程。 請考慮透過此服務公開的資訊，作為在 VM 內執行之所有應用程式的共用資訊。

## <a name="usage"></a>使用方式

### <a name="access-azure-instance-metadata-service"></a>存取 Azure Instance Metadata Service

若要存取 IMDS，請從 [Azure Resource Manager](/rest/api/resources/) 或 [Azure 入口網站](https://portal.azure.com)建立 VM，並使用下列範例。
如需更多範例，請參閱 [Azure 實例中繼資料範例](https://github.com/microsoft/azureimds)。

以下是用來取得實例之所有中繼資料的範例程式碼。 若要存取特定的資料來源，請參閱「 [中繼資料 API](#metadata-apis) 」一節。 

**要求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance?api-version=2020-09-01 | ConvertTo-Json
```
> [!NOTE]
> `-NoProxy`旗標只適用于 PowerShell 6 或更新版本。 如果您沒有 proxy 設定，您可以省略旗標。

**回應**

> [!NOTE]
> 回應是 JSON 字串。 透過 Cmdlet 將 REST 查詢輸送 `ConvertTo-Json` 以進行整齊列印。

```json
{
    "compute": {
        "azEnvironment": "AZUREPUBLICCLOUD",
        "isHostCompatibilityLayerVm": "true",
        "licenseType":  "Windows_Client",
        "location": "westus",
        "name": "examplevmname",
        "offer": "Windows",
        "osProfile": {
            "adminUsername": "admin",
            "computerName": "examplevmname",
            "disablePasswordAuthentication": "true"
        },
        "osType": "linux",
        "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
        "plan": {
            "name": "planName",
            "product": "planProduct",
            "publisher": "planPublisher"
        },
        "platformFaultDomain": "36",
        "platformUpdateDomain": "42",
        "publicKeys": [{
                "keyData": "ssh-rsa 0",
                "path": "/home/user/.ssh/authorized_keys0"
            },
            {
                "keyData": "ssh-rsa 1",
                "path": "/home/user/.ssh/authorized_keys1"
            }
        ],
        "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
        "resourceGroupName": "macikgo-test-may-23",
        "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
        "securityProfile": {
            "secureBootEnabled": "true",
            "virtualTpmEnabled": "false"
        },
        "sku": "Windows-Server-2012-R2-Datacenter",
        "storageProfile": {
            "dataDisks": [{
                "caching": "None",
                "createOption": "Empty",
                "diskSizeGB": "1024",
                "image": {
                    "uri": ""
                },
                "lun": "0",
                "managedDisk": {
                    "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampledatadiskname",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }],
            "imageReference": {
                "id": "",
                "offer": "UbuntuServer",
                "publisher": "Canonical",
                "sku": "16.04.0-LTS",
                "version": "latest"
            },
            "osDisk": {
                "caching": "ReadWrite",
                "createOption": "FromImage",
                "diskSizeGB": "30",
                "diffDiskSettings": {
                    "option": "Local"
                },
                "encryptionSettings": {
                    "enabled": "false"
                },
                "image": {
                    "uri": ""
                },
                "managedDisk": {
                    "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampleosdiskname",
                "osType": "Linux",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }
        },
        "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
        "tags": "baz:bash;foo:bar",
        "version": "15.05.22",
        "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
        "vmScaleSetName": "crpteste9vflji9",
        "vmSize": "Standard_A3",
        "zone": ""
    },
    "network": {
        "interface": [{
            "ipv4": {
               "ipAddress": [{
                    "privateIpAddress": "10.144.133.132",
                    "publicIpAddress": ""
                }],
                "subnet": [{
                    "address": "10.144.133.128",
                    "prefix": "26"
                }]
            },
            "ipv6": {
                "ipAddress": [
                 ]
            },
            "macAddress": "0011AAFFBB22"
        }]
    }
}
```

### <a name="data-output"></a>資料輸出

根據預設，IMDS 會以 JSON 格式傳回 (`Content-Type: application/json`) 的資料。 不過，某些 Api 可能會傳回不同格式的資料（如有要求）。
下表列出 Api 可能支援的其他資料格式。

API | 預設資料格式 | 其他格式
--------|---------------------|--------------
/attested | json | 無
/identity | json | 無
/instance | json | text
/scheduledevents | json | 無

若要存取非預設的回應格式，請指定要求的格式作為要求中的查詢字串參數。 例如：

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> 若為中的分葉節點 `/metadata/instance` ，則 `format=json` 無法運作。 針對這些查詢， `format=text` 必須明確指定，因為預設格式為 JSON。

### <a name="version"></a>版本

IMDS 已建立版本，並在 HTTP 要求中指定 API 版本是必要的。

支援的 API 版本為： 
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01

> [!NOTE]
> 版本2020-10-01 可能還無法在每個區域中使用。

新增較新版本時，如果您的腳本相依于特定資料格式，您仍然可以存取較舊版本的相容性。

當您未指定版本時，您會收到錯誤，其中包含最新支援版本的清單。

> [!NOTE]
> 回應是 JSON 字串。 下列範例指出未指定版本時的錯誤狀況。 為了方便閱讀，回應會有很美觀的列印。

**要求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance
```

**回應**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

## <a name="metadata-apis"></a>中繼資料 API

IMDS 包含多個代表不同資料來源的 Api。

API | 描述 | 引進的版本
----|-------------|-----------------------
/attested | 查看 [證明資料](#attested-data) | 2018-10-01
/identity | 請參閱[取得存取權杖](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
/instance | 請參閱[執行個體 API](#instance-api) | 2017-04-02
/scheduledevents | 查看 [排定的事件](scheduled-events.md) | 2017-08-01

## <a name="instance-api"></a>執行個體 API

實例 API 會公開 VM 實例的重要中繼資料，包括 VM、網路和儲存體。 您可以透過下列類別來存取下列類別 `instance/compute` ：

資料 | 描述 | 引進的版本
-----|-------------|-----------------------
azEnvironment | VM 執行所在的 Azure 環境。 | 2018-10-01
customData | 這項功能目前已停用。 | 2019-02-01
isHostCompatibilityLayerVm | 識別 VM 是否會在主機相容性層上執行。 | 2020-06-01
licenseType | [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit)的授權類型。 請注意，只有啟用 AHB 的 Vm 才會出現此情況。 | 2020-09-01
location | VM 執行所在的 Azure 區域。 | 2017-04-02
NAME | VM 的名稱。 | 2017-04-02
供應項目 | 提供 VM 映像的資訊。 這僅適用于從 Azure 映射庫部署的映射。 | 2017-04-02
osProfile.adminUsername | 指定系統管理員帳戶的名稱。 | 2020-07-15
osProfile。 computerName | 指定電腦的名稱。 | 2020-07-15
osProfile. disablePasswordAuthentication | 指定密碼驗證是否停用。 請注意，這只適用于 Linux Vm。 | 2020-10-01
osType | Linux 或 Windows。 | 2017-04-02
placementGroupId | 虛擬機器擴展集的[放置群組](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)。 | 2017-08-01
計劃 | 如果 VM 是 Azure Marketplace 映射，則包含其名稱、產品和發行者的[方案](/rest/api/compute/virtualmachines/createorupdate#plan)。 | 2018-04-02
platformUpdateDomain |  [更新](../manage-availability.md) VM 執行所在的網域。 | 2017-04-02
platformFaultDomain | VM 執行所在的[容錯網域](../manage-availability.md)。 | 2017-04-02
provider | VM 的提供者。 | 2018-10-01
publicKeys | 指派給 VM 和路徑[的公用金鑰集合](/rest/api/compute/virtualmachines/createorupdate#sshpublickey)。 | 2018-04-02
publisher | VM 映射的發行者。 | 2017-04-02
resourceGroupName | VM 的[資源群組](../../azure-resource-manager/management/overview.md)。 | 2017-08-01
resourceId | 資源的 [完整](/rest/api/resources/resources/getbyid) 識別碼。 | 2019-03-11
sku | VM 映射的特定 SKU。 | 2017-04-02
securityProfile. secureBootEnabled | 識別是否在 VM 上啟用 UEFI 安全開機。 | 2020-06-01
securityProfile.virtualTpmEnabled | 識別是否在 VM 上啟用虛擬的可信賴平臺模組 (TPM) 。 | 2020-06-01
storageProfile | 請參閱 [儲存體設定檔](#storage-metadata)。 | 2019-06-01
subscriptionId | 適用于 VM 的 Azure 訂用帳戶。 | 2017-08-01
tags | VM 的[標記](../../azure-resource-manager/management/tag-resources.md)。  | 2017-08-01
tagsList | 格式化為 JSON 陣列的標記，可讓您更輕鬆地進行程式設計剖析。  | 2019-06-04
version | VM 映射的版本。 | 2017-04-02
vmId | VM 的[唯一識別碼](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)。 | 2017-04-02
vmScaleSetName | 虛擬機器擴展集的[虛擬機器擴展集名稱](../../virtual-machine-scale-sets/overview.md)。 | 2017-12-01
vmSize | 請參閱 [VM 大小](../sizes.md)。 | 2017-04-02
區域 | VM 的[可用性區域](../../availability-zones/az-overview.md)。 | 2017-12-01

### <a name="sample-1-track-a-vm-running-on-azure"></a>範例1：追蹤在 Azure 上執行的 VM

如果您是服務提供者，您可能需要追蹤執行軟體的 Vm 數目，或有需要追蹤 VM 唯一性的代理程式。 若要能夠取得 VM 的唯一識別碼，請使用 `vmId` IMDS 中的欄位。

**要求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**回應**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-different-data-replicas"></a>範例2：放置不同的資料複本

對於特定案例，不同資料複本的放置相當重要。 例如，透過[orchestrator](https://kubernetes.io/docs/user-guide/node-selection/)的[HDFS 複本放置](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps)或容器放置可能會要求您知道 `platformFaultDomain` 和 `platformUpdateDomain` VM 正在執行。
您也可以使用適用於執行個體的[可用性區域](../../availability-zones/az-overview.md)來做出這些決定。
您可以直接透過 IMDS 來查詢此資料。

**要求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**回應**

```text
0
```

### <a name="sample-3-get-more-information-about-the-vm-during-support-case"></a>範例3：在支援案例中取得 VM 的詳細資訊

如果您是服務提供者，您可能會收到支援呼叫，而您想要瞭解更多 VM 的相關資訊。 在此情況下，要求客戶共用計算中繼資料會很有用。

**要求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01
```

**回應**

> [!NOTE]
> 回應是 JSON 字串。 下列範例回應均列印清晰，很容易閱讀。

```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "Windows",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "Windows-Server-2012-R2-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

### <a name="sample-4-get-the-azure-environment-where-the-vm-is-running"></a>範例4：取得 VM 執行所在的 Azure 環境

Azure 有各種主權雲端，例如 [Azure Government](https://azure.microsoft.com/overview/clouds/government/)。 有時，您需要 Azure 環境來進行一些執行時間決策。 下列範例會說明如何實現此行為。

**要求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**回應**

```text
AzurePublicCloud
```

此處會列出雲端和 Azure 環境的值。

 Cloud   | Azure 環境
---------|-----------------
[所有正式推出的全球 Azure 區域](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>網路中繼資料 

網路中繼資料是執行個體 API 的一部分。 您可以透過端點取得下列網路類別 `instance/network` 。

資料 | 描述 | 引進的版本
-----|-------------|-----------------------
ipv4/privateIpAddress | VM 的本機 IPv4 位址。 | 2017-04-02
ipv4/publicIpAddress | VM 的公用 IPv4 位址。 | 2017-04-02
subnet/address | VM 的子網位址。 | 2017-04-02
subnet/prefix | 子網前置詞。 範例：24 | 2017-04-02
ipv6/ipAddress | VM 的本機 IPv6 位址。 | 2017-04-02
macAddress | VM mac 位址。 | 2017-04-02

> [!NOTE]
> 所有 API 回應都是 JSON 字串。 下列所有範例回應均列印清晰，很容易閱讀。

#### <a name="sample-1-retrieve-network-information"></a>範例1：取出網路資訊

**要求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/network?api-version=2017-08-01
```

**回應**

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="sample-2-retrieve-public-ip-address"></a>範例2：取出公用 IP 位址

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>儲存體中繼資料

儲存體中繼資料是在端點下的實例 API 的一部分 `instance/compute/storageProfile` 。
其會對與 VM 相關聯的儲存體磁碟提供詳細資料。 

VM 的儲存體設定檔分為三種類別：映射參考、作業系統磁片和資料磁片。

映射參考物件包含下列有關作業系統映射的資訊：

資料    | 描述
--------|-----------------
id      | 資源識別碼
供應項目   | 平臺或映射的供應專案
publisher | 映像發行者
sku     | 映像 SKU
version | 平臺或映射的版本

作業系統磁片物件包含下列有關 VM 所用作業系統磁片的資訊：

資料    | 描述
--------|-----------------
快取 | 快取需求
createOption | 如何建立 VM 的相關資訊
diffDiskSettings | 暫時性磁碟設定
diskSizeGB | 磁碟的大小 (以 GB 為單位)
encryptionSettings | 磁碟的加密設定
image   | 來源使用者映像虛擬硬碟
ManagedDisk | 受控磁碟參數
NAME    | 磁碟名稱
osType  | 磁片中包含的作業系統類型
vhd     | 虛擬硬碟
writeAcceleratorEnabled | 是否已 `writeAccelerator` 在磁片上啟用

資料磁碟陣列包含附加至 VM 的資料磁碟清單。 每個資料磁碟物件都包含下列資訊：

資料    | 描述
--------|-----------------
快取 | 快取需求
createOption | 如何建立 VM 的相關資訊
diffDiskSettings | 暫時性磁碟設定
diskSizeGB | 磁碟的大小 (以 GB 為單位)
image   | 來源使用者映像虛擬硬碟
lun     | 磁碟的邏輯單元編號
ManagedDisk | 受控磁碟參數
NAME    | 磁碟名稱
vhd     | 虛擬硬碟
writeAcceleratorEnabled | 是否已 `writeAccelerator` 在磁片上啟用

下列範例顯示如何查詢 VM 的儲存體資訊。

**要求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01
```

**回應**

> [!NOTE]
> 回應是 JSON 字串。 下列範例回應均列印清晰，很容易閱讀。

```json
{
    "dataDisks": [
      {
        "caching": "None",
        "createOption": "Empty",
        "diskSizeGB": "1024",
        "image": {
          "uri": ""
        },
        "lun": "0",
        "managedDisk": {
          "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
          "storageAccountType": "Standard_LRS"
        },
        "name": "exampledatadiskname",
        "vhd": {
          "uri": ""
        },
        "writeAcceleratorEnabled": "false"
      }
    ],
    "imageReference": {
      "id": "",
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04.0-LTS",
      "version": "latest"
    },
    "osDisk": {
      "caching": "ReadWrite",
      "createOption": "FromImage",
      "diskSizeGB": "30",
      "diffDiskSettings": {
        "option": "Local"
      },
      "encryptionSettings": {
        "enabled": "false"
      },
      "image": {
        "uri": ""
      },
      "managedDisk": {
        "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
        "storageAccountType": "Standard_LRS"
      },
      "name": "exampleosdiskname",
      "osType": "Linux",
      "vhd": {
        "uri": ""
      },
      "writeAcceleratorEnabled": "false"
    }
}
```

## <a name="vm-tags"></a>虛擬機器標籤

VM 標記會包含在端點下的實例 API `instance/compute/tags` 。
標記可能已套用至您的 Azure VM，以邏輯方式將它們組織成分類法。 您可以使用下列要求來取出指派給 VM 的標記。

**要求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**回應**

```text
Department:IT;Environment:Test;Role:WebRole
```

`tags` 欄位是字串，其中包含以分號分隔的標記。 如果標記本身使用分號，此輸出可能會造成問題。 如果將剖析器撰寫成以程式設計方式解壓縮標記，您應該依賴 `tagsList` 欄位。 此 `tagsList` 欄位是沒有分隔符號的 JSON 陣列，因此更容易剖析。

**要求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04
```

**回應**

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "Environment",
    "value": "Test"
  },
  {
    "name": "Role",
    "value": "WebRole"
  }
]
```

## <a name="attested-data"></a>證明資料

IMDS 可協助確保提供的資料來自 Azure。 Microsoft 會簽署這項資訊的一部分，因此您可以確認 Azure Marketplace 中的映射是您正在 Azure 上執行的映射。

### <a name="sample-1-get-attested-data"></a>範例1：取得證明資料

> [!NOTE]
> 所有 API 回應都是 JSON 字串。 下列範例回應均美化顯示，很容易閱讀。

**要求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

> [!NOTE]
> 由於 IMDS 的快取機制，可能會傳回先前快取的 `nonce` 值。

`Api-version` 是必要欄位。 如需支援的 API 版本，請參閱[使用方式一節](#usage)。
`Nonce` 這是選擇性的10位數位符串。 如果未提供，IMDS 會在其位置傳回目前的國際標準時間時間戳記。

**回應**

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

簽名 blob 是檔的 [pkcs7](https://aka.ms/pkcs7)簽署版本。 它包含用於簽署的憑證，以及特定 VM 特定的詳細資料。 

針對使用 Azure Resource Manager 所建立的 vm，這包括 `vmId` 、、 `sku` `nonce` 、 `subscriptionId` 、 `timeStamp` 適用于檔的建立和到期，以及影像的計畫資訊。 Azure Marketplace 映像才會填入方案資訊。 

針對使用傳統部署模型所建立的 Vm，只 `vmId` 保證會填入。 您可以從回應中解壓縮憑證，並使用它來確認回應有效且來自 Azure。

檔包含下欄欄位：

資料 | 描述 | 引進的版本
-----|-------------|-----------------------
licenseType | [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit)的授權類型。 請注意，只有啟用 AHB 的 Vm 才會出現此情況。 | 2020-09-01
nonce | 可選擇性隨要求提供的字串。 如果未 `nonce` 提供，則會使用目前的國際標準時間時間戳記。 | 2018-10-01
計劃 | [Azure Marketplace 的影像方案](/rest/api/compute/virtualmachines/createorupdate#plan)。 包含方案識別碼 (名稱) 、產品影像或供應專案 (產品) ，以及發行者識別碼 (發行者) 。 | 2018-10-01
timestamp/createdOn | 建立簽署的檔時的國際標準時間時間戳記。 | 2018-20-01
timestamp/expiresOn | 簽署的檔到期時的國際標準時間時間戳記。 | 2018-10-01
vmId |  VM 的[唯一識別碼](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)。 | 2018-10-01
subscriptionId | VM 的 Azure 訂用帳戶。 | 2019-04-30
sku | VM 映射的特定 SKU。 | 2019-11-01

### <a name="sample-2-validate-that-the-vm-is-running-in-azure"></a>範例2：驗證 VM 是否正在 Azure 中執行

Azure Marketplace 中的廠商希望確保其軟體僅能在 Azure 中執行。 如果有人將 VHD 複製到內部部署環境，則廠商必須能夠偵測到此情況。 透過 IMDS，這些廠商可以取得簽署的資料，以保證回應只會來自 Azure。

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

確認簽章是來自 Microsoft Azure，並檢查憑證鏈是否有錯誤。

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

> [!NOTE]
> 由於 IMDS 的快取機制，可能會傳回先前快取的 `nonce` 值。

`nonce`如果您在初始要求中提供參數，則可以比較已簽署檔中的 `nonce` 。

> [!NOTE]
> 公用雲端和每個主權雲端的憑證將會不同。

Cloud | 憑證
------|------------
[所有正式推出的全球 Azure 區域](https://azure.microsoft.com/regions/) | *.metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)          | *.metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)     | *.metadata.azure.cn
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                | *.metadata.microsoftazure.de

> [!NOTE]
> 憑證可能不會完全符合 `metadata.azure.com` 公用雲端。 基於這個理由，憑證驗證應該允許來自任何子域的一般名稱 `.metadata.azure.com` 。

在驗證期間由於網路限制而無法下載中繼憑證的情況下，您可以釘選中繼憑證。 請注意，Azure 會匯總憑證，這是標準的 PKI 實務。 當變換發生時，您需要更新釘選的憑證。 每當規劃更新中繼憑證的變更時，就會更新 Azure blog，並通知 Azure 客戶。 

您可以在 [PKI 存放庫](https://www.microsoft.com/pki/mscorp/cps/default.htm)中找到中繼憑證。 每個區域的中繼憑證可能不同。

> [!NOTE]
> Azure 中國世紀的中繼憑證是來自 DigiCert Global 根 CA，而不是巴爾的摩。
如果您將 Azure 中國的中繼憑證釘選在根鏈授權單位變更中，則必須更新中繼憑證。

## <a name="failover-clustering-in-windows-server"></a>Windows Server 中的容錯移轉叢集

當您使用容錯移轉叢集查詢 IMDS 時，有時需要將路由新增至路由表。 其做法如下：

1. 使用系統管理員權限開啟命令提示字元。

1. 執行下列命令，並記下網路目的地的介面位址 (`0.0.0.0`) 在 IPv4 路由表中。

```bat
route print
```

> [!NOTE]
> 下列範例輸出來自已啟用容錯移轉叢集的 Windows Server VM。 為了簡單起見，輸出只會包含 IPv4 路由表。

```text
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

執行下列命令，並使用網路目的地的介面位址 (`0.0.0.0`) ，這 `10.0.1.10` 在此範例中)  (。

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

## <a name="managed-identity"></a>受控識別

您可以在 VM 上啟用受控識別（由系統指派）。 您也可以將一或多個使用者指派的受控識別指派給 VM。
然後，您可以從 IMDS 要求受控識別的權杖。 使用這些權杖來向其他 Azure 服務（例如 Azure Key Vault）進行驗證。

如需啟用這項功能的詳細步驟，請參閱[取得存取權杖](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)。

## <a name="scheduled-events"></a>排定的事件
您可以使用 IMDS 取得已排程事件的狀態。 然後，使用者可以指定要在這些事件上執行的一組動作。 如需詳細資訊，請參閱 [排程的事件](scheduled-events.md)。 

## <a name="regional-availability"></a>區域可用性

服務已在所有 Azure 雲端中正式推出。

## <a name="sample-code-in-different-languages"></a>不同語言的範例程式碼

下表列出在 VM 內使用不同語言呼叫 IMDS 的範例：

Language      | 範例
--------------|----------------
C++ (Windows) | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#            | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Go            | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Java          | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
NodeJS        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Perl          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
PowerShell    | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Puppet        | https://github.com/keirans/azuremetadata
Python        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
Ruby          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Visual Basic  | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb

## <a name="errors-and-debugging"></a>錯誤和調試

如果找不到資料元素或要求格式不正確，IMDS 會傳回標準 HTTP 錯誤。 例如：

HTTP 狀態碼 | 原因
-----------------|-------
200 確定 |
400 不正確的要求 | `Metadata: true` `format=json` 查詢分葉節點時，遺漏標頭或遺漏參數。
404 找不到  | 要求的元素不存在。
405 不允許的方法 | 僅 `GET` 支援要求。
410 不存在 | 請在一段時間後重試，最多70秒。
429 要求太多 | API 目前最多支援每秒5個查詢。
500 服務錯誤     | 請稍後再重試。

### <a name="frequently-asked-questions"></a>常見問題集

**我收到錯誤 `400 Bad Request, Required metadata header not specified` 。這代表什麼意思？**

IMDS 需要在 `Metadata: true` 要求中傳遞標頭。 在 REST 呼叫中傳遞此標頭可允許存取 IMDS。

**為什麼我沒有收到我的 VM 計算資訊？**

目前，IMDS 僅支援使用 Azure Resource Manager 所建立的實例。

**我在一段時間前 Azure Resource Manager 建立了 VM。為什麼我看不到計算中繼資料資訊？**

如果您在2016年9月之後建立 VM，請新增 [標記](../../azure-resource-manager/management/tag-resources.md) 以開始查看計算中繼資料。 如果您在2016年9月之前建立 VM，請在 VM 實例中新增或移除擴充功能或資料磁片，以重新整理中繼資料。

**為什麼我看不到為新版本填入的所有資料？**

如果您在2016年9月之後建立 VM，請新增 [標記](../../azure-resource-manager/management/tag-resources.md) 以開始查看計算中繼資料。 如果您在2016年9月之前建立 VM，請在 VM 實例中新增或移除擴充功能或資料磁片，以重新整理中繼資料。

**為什麼會收到錯誤 `500 Internal Server Error` 或 `410 Resource Gone` ？**

請重試您的要求。 如需詳細資訊，請參閱 [暫時性錯誤處理](/azure/architecture/best-practices/transient-faults)。 如果問題持續發生，請在 VM 的 Azure 入口網站中建立支援問題。

**這是否適用于虛擬機器擴展集實例？**

是，IMDS 可供虛擬機器擴展集實例使用。

**我已更新虛擬機器擴展集中的標籤，但 (與單一實例 Vm) 不會出現在實例中。我是否發生錯誤？**

虛擬機器擴展集目前的標籤只會在重新開機、重新安裝映射或磁片變更至實例時向 VM 顯示。

**為什麼我的要求會在呼叫服務時停止？**

中繼資料呼叫必須從指派給 VM 主要網路卡的主要 IP 位址進行。 此外，如果您已變更路由，則在 VM 的本機路由表中必須有 169.254.169.254/32 位址的路由。
   * <details>
        <summary>驗證您的路由表</summary>

        1. 傾印您的本機路由表，並尋找 IMDS 專案。 例如：
            ```console
            > route print
            IPv4 Route Table
            ===========================================================================
            Active Routes:
            Network Destination        Netmask          Gateway       Interface  Metric
                      0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
                    127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
                    127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
              127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
                168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
              169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
            ... (continues) ...
            ```
        1. 確認有路由存在 `169.254.169.254` ，並記下對應的網路介面 (例如 `172.16.69.7`) 。
        1. 傾印介面設定，並尋找對應至路由表中所參考的介面，並記下 MAC (實體) 位址。
            ```console
            > ipconfig /all
            ... (continues) ...
            Ethernet adapter Ethernet:

               Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
               Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
               Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
               DHCP Enabled. . . . . . . . . . . : Yes
               Autoconfiguration Enabled . . . . : Yes
               Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
               IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
               Subnet Mask . . . . . . . . . . . : 255.255.255.0
            ... (continues) ...
            ```
        1. 確認介面對應至 VM 的主要 NIC 和主要 IP。 您可以藉由查看 Azure 入口網站中的網路設定，或查看 Azure CLI 來尋找主要 NIC 和 IP。 請注意公用和私人 Ip (以及 MAC 位址（如果您使用的是 CLI) ）。 以下是 PowerShell CLI 範例：
            ```powershell
            $ResourceGroup = '<Resource_Group>'
            $VmName = '<VM_Name>'
            $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
            foreach($NicName in $NicNames)
            {
                $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
                Write-Host $NicName, $Nic.primary, $Nic.macAddress
            }
            # Output: wintest767 True 00-0D-3A-E5-1C-C0
            ```
        1. 如果不相符，請更新路由表，以便將主要 NIC 和 IP 設為目標。
    </details>

## <a name="support"></a>支援

如果您在多次嘗試之後無法取得中繼資料回應，您可以在 Azure 入口網站中建立支援問題。
針對 **問題類型**，請選取 [ **管理**]。 在 [ **類別**] 中，選取 [ **Instance Metadata Service**]。

![Instance Metadata Service 支援的螢幕擷取畫面](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>後續步驟

[取得 VM 的存取權杖](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

[排定的事件](scheduled-events.md)
