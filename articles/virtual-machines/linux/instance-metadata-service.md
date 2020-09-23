---
title: Azure 執行個體中繼資料服務
description: 瞭解 Azure Instance Metadata Service，以及它如何提供有關目前執行中虛擬機器實例的資訊。
services: virtual-machines
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/29/2020
ms.author: sukumari
ms.reviewer: azmetadatadev
ms.openlocfilehash: ea11e2f5f8d89381723011686de9e22639997c01
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90974147"
---
# <a name="azure-instance-metadata-service-imds"></a>Azure Instance Metadata Service (IMDS) 

Azure Instance Metadata Service (IMDS) 提供目前執行中虛擬機器執行個體的相關資訊，而且可用來管理和設定您的虛擬機器。
此資訊包括 SKU、儲存體、網路設定和即將進行的維護事件。 如需可用資料的完整清單，請參閱[中繼資料 API](#metadata-apis)。
Instance Metadata Service 可用於執行虛擬機器和虛擬機器擴展集實例。 所有 Api 都支援使用 [Azure Resource Manager](/rest/api/resources/)建立/管理的 vm。 只有證明和網路端點支援傳統 (非 ARM) Vm，而證明則只會提供有限的範圍。

Azure 的 IMDS 是 REST 端點，可在已知的非可路由 IP 位址 (`169.254.169.254`) ，且只能從 VM 記憶體取。 VM 與 IMDS 之間的通訊絕不會離開主機。
在查詢 IMDS 並將其視為相同時，最佳作法是讓您的 HTTP 用戶端略過 VM 內的 web proxy `169.254.169.254` [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md) 。

## <a name="security"></a>安全性

執行個體中繼資料服務端點只能從非可路由 IP 位址上的執行中虛擬機器執行個體內存取。 此外，服務會拒絕任何具有 `X-Forwarded-For` 標頭的要求。
要求也必須包含 `Metadata: true` 標頭，以確認直接預期實際要求，而不是非預期重新導向的一部分。

> [!IMPORTANT]
> Instance Metadata Service 不是敏感性資料的通道。 端點會開放給 VM 上的所有流程。 透過此服務公開的資訊，應視為 VM 內所有執行中應用程式的共用資訊。

## <a name="usage"></a>使用量

### <a name="accessing-azure-instance-metadata-service"></a>存取 Azure Instance Metadata Service

若要存取 Instance Metadata Service，請從 [Azure Resource Manager](/rest/api/resources/) 或 [Azure 入口網站](https://portal.azure.com)中建立 VM，並遵循以下的範例。
如需如何查詢 IMDS 的其他範例，請參閱 [Azure 執行個體中繼資料範例](https://github.com/microsoft/azureimds)。

以下是擷取取執行個體所有中繼資料的範例程式碼，若要存取特定資料來源，請參閱[中繼資料 API](#metadata-apis) 一節。 

**要求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-06-01"
```

**回應**

> [!NOTE]
> 回應是 JSON 字串。 下列範例回應均列印清晰，很容易閱讀。

```json
{
    "compute": {
        "azEnvironment": "AZUREPUBLICCLOUD",
        "isHostCompatibilityLayerVm": "true",
        "location": "westus",
        "name": "examplevmname",
        "offer": "Windows",
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
        "resourceId": "/subscriptions/8d10da13-8125-4ba9-a717-bf7490507b3d/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
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
                    "id": "/subscriptions/8d10da13-8125-4ba9-a717-bf7490507b3d/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
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
                    "id": "/subscriptions/8d10da13-8125-4ba9-a717-bf7490507b3d/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
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
        "subscriptionId": "8d10da13-8125-4ba9-a717-bf7490507b3d",
        "tags": "baz:bash;foo:bar",
        "version": "15.05.22",
        "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
        "vmScaleSetName": "crpteste9vflji9",
        "vmSize": "Standard_A3",
        "zone": ""
    }
}
```

### <a name="data-output"></a>資料輸出

根據預設，執行個體中繼資料服務會以 JSON 格式傳回資料 (`Content-Type: application/json`)。 不過，某些 Api 可以傳回不同格式的資料（如有要求）。
下表是 API 可能支援之其他資料格式的參考。

API | 預設資料格式 | 其他格式
--------|---------------------|--------------
/attested | json | 無
/identity | json | 無
/instance | json | text
/scheduledevents | json | 無

若要存取非預設的回應格式，請指定要求的格式作為要求中的查詢字串參數。 例如：

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> 若是/metadata/instance 中的分葉節點，則 `format=json` 無法運作。 必須明確指定這些查詢， `format=text` 因為預設格式為 json。

### <a name="versioning"></a>版本控制

Instance Metadata Service 已版本化，因此在 HTTP 要求中指定 API 版本是強制的。

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

請注意，發行新版本時，將需要一段時間才能推廣至所有區域。

新增較新版本時，如果您的指令碼對於特定資料格式有相依性，則因為相容性而仍可存取較舊版本。

若未指定任何版本，則會傳回錯誤，其中包含最新支援版本的清單。

> [!NOTE]
> 回應是 JSON 字串。 下列範例指出未指定版本時的錯誤狀況，而且回應列印清晰，很容易閱讀。

**要求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance"
```

**回應**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2018-10-01",
        "2018-04-02",
        "2018-02-01"
    ]
}
```

## <a name="metadata-apis"></a>中繼資料 API

Metadata Service 包含多個代表不同資料來源的 Api。

API | 描述 | 引進的版本
----|-------------|-----------------------
/attested | 請參閱[證明資料](#attested-data) | 2018-10-01
/identity | 請參閱[取得存取權杖](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
/instance | 請參閱[執行個體 API](#instance-api) | 2017-04-02
/scheduledevents | 請參閱[排定的事件](scheduled-events.md) | 2017-08-01

## <a name="instance-api"></a>執行個體 API

實例 API 會公開 VM 實例的重要中繼資料，包括 VM、網路和儲存體。 您可以透過執行個體/計算來存取下列類別：

資料 | 描述 | 引進的版本
-----|-------------|-----------------------
azEnvironment | VM 執行所在的 Azure 環境 | 2018-10-01
customData | 這項功能目前已停用。 當此檔可用時，我們會更新此檔 | 2019-02-01
isHostCompatibilityLayerVm | 識別 VM 是否在主機相容性層上執行 | 2020-06-01
location | VM 執行所在的 Azure 區域 | 2017-04-02
NAME | VM 的名稱 | 2017-04-02
供應項目 | 提供 VM 映像的資訊，而且只針對從 Azure 映像資源庫部署的映像呈現 | 2017-04-02
osType | Linux 或 Windows | 2017-04-02
placementGroupId | 虛擬機器擴展集的[放置群組](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) | 2017-08-01
計劃 | 如果[方案](/rest/api/compute/virtualmachines/createorupdate#plan)是 Azure Marketplace 映像，則其會包含 VM 的名稱、產品及發行者 | 2018-04-02
platformUpdateDomain |  VM 執行所在的[更新網域](manage-availability.md) | 2017-04-02
platformFaultDomain | VM 執行所在的[容錯網域](manage-availability.md) | 2017-04-02
provider | VM 的提供者 | 2018-10-01
publicKeys | 指派給 VM 和路徑的[公開金鑰集合](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) | 2018-04-02
publisher | VM 映像的發佈者 | 2017-04-02
resourceGroupName | 虛擬機器的[資源群組](../../azure-resource-manager/management/overview.md) | 2017-08-01
resourceId | 資源的[完整](/rest/api/resources/resources/getbyid) ID | 2019-03-11
sku | VM 映像的特定 SKU | 2017-04-02
securityProfile. secureBootEnabled | 識別 VM 上是否已啟用 UEFI 安全開機 | 2020-06-01
securityProfile.virtualTpmEnabled | 識別是否在 VM 上啟用虛擬的可信賴平臺模組 (TPM)  | 2020-06-01
storageProfile | 請參閱[儲存體設定檔](#storage-metadata) | 2019-06-01
subscriptionId | 虛擬機器的 Azure 訂用帳戶 | 2017-08-01
tags | 虛擬機器的[標籤](../../azure-resource-manager/management/tag-resources.md)  | 2017-08-01
tagsList | 格式化為 JSON 陣列以方便透過程式設計剖析的標籤  | 2019-06-04
version | VM 映像的版本 | 2017-04-02
vmId | VM 的[唯一識別碼](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2017-04-02
vmScaleSetName | 虛擬機器擴展集的[虛擬機器擴展集名稱](../../virtual-machine-scale-sets/overview.md) | 2017-12-01
vmSize | [VM 大小](../sizes.md) | 2017-04-02
區域 | 您虛擬機器的[可用性區域](../../availability-zones/az-overview.md) | 2017-12-01

### <a name="sample-1-tracking-vm-running-on-azure"></a>範例 1：追蹤在 Azure 上執行的 VM

身為服務提供者，您可能需要追蹤執行軟體的 VM 數目，或者具有需要追蹤 VM 唯一性的代理程式。 若要能夠取得 VM 的唯一識別碼，請從執行個體中繼資料服務使用 `vmId` 欄位。

**要求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**回應**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-containers-data-partitions-based-faultupdate-domain"></a>範例 2：容器的位置，資料分割基礎容錯/更新網域

對於特定案例，不同資料複本的放置相當重要。 例如 [HDFS 複本放置](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps)，或透過 [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) 的容器放置，您需要知道 VM 執行所在的 `platformFaultDomain` 和 `platformUpdateDomain`。
您也可以使用適用於執行個體的[可用性區域](../../availability-zones/az-overview.md)來做出這些決定。
您可以直接透過執行個體中繼資料服務查詢此資料。

**要求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**回應**

```text
0
```

### <a name="sample-3-getting-more-information-about-the-vm-during-support-case"></a>範例 3：取得支援案例期間 VM 的相關詳細資訊

身為服務提供者，您可能會收到支援呼叫，而您想要知道更多 VM 的相關資訊。 要求客戶共用計算中繼資料可以為支援專業人員提供基本資訊，以了解 Azure 上的 VM 種類。

**要求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01"
```

**回應**

> [!NOTE]
> 回應是 JSON 字串。 下列範例回應均列印清晰，很容易閱讀。

```json
{
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
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
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
}
```

### <a name="sample-4-getting-azure-environment-where-the-vm-is-running"></a>範例 4：取得 VM 執行所在的 Azure 環境

Azure 有各種不同的主權雲端，例如 [Azure Government](https://azure.microsoft.com/overview/clouds/government/)。 有時候您會需要 Azure 環境來進行一些執行階段決策。 下列範例會說明如何實現此行為。

**要求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**回應**

```text
AzurePublicCloud
```

以下列出雲端和 Azure 環境的值。

 Cloud   | Azure 環境
---------|-----------------
[所有正式推出的全域 Azure 區域](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>網路中繼資料 

網路中繼資料是執行個體 API 的一部分。 可透過執行個體/網路端點取得下列網路類別。

資料 | 描述 | 引進的版本
-----|-------------|-----------------------
ipv4/privateIpAddress | VM 的本機 IPv4 位址 | 2017-04-02
ipv4/publicIpAddress | VM 的公用 IPv4 位址 | 2017-04-02
subnet/address | VM 的子網路位址 | 2017-04-02
subnet/prefix | 子網路首碼，範例 24 | 2017-04-02
ipv6/ipAddress | VM 的本機 IPv6 位址 | 2017-04-02
macAddress | VM mac 位址 | 2017-04-02

> [!NOTE]
> 所有 API 回應都是 JSON 字串。 下列所有範例回應均列印清晰，很容易閱讀。

#### <a name="sample-1-retrieving-network-information"></a>範例 1：擷取網路資訊

**要求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**回應**

> [!NOTE]
> 回應是 JSON 字串。 下列範例回應均列印清晰，很容易閱讀。

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

#### <a name="sample-2-retrieving-public-ip-address"></a>範例 2：擷取公用 IP 位址

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>儲存體中繼資料

儲存體中繼資料屬於執行個體/計算/storageProfile 端點下的執行個體 API。
其會對與 VM 相關聯的儲存體磁碟提供詳細資料。 

VM 的儲存體設定檔分為三種類別：映射參考、OS 磁片和資料磁片。

映像參考物件包含下列作業系統映像的相關資訊：

資料    | 描述
--------|-----------------
id      | 資源識別碼
供應項目   | 平台或市集映像的供應項目
publisher | 映像發行者
sku     | 映像 sku
version | 平台或市集映像的版本

OS 磁碟物件包含有關 VM 所使用 OS 磁碟的下列資訊：

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
writeAcceleratorEnabled | 是否在磁碟上啟用 writeAccelerator

資料磁碟陣列包含附加至 VM 的資料磁碟清單。 每個資料磁碟物件都包含下列資訊：

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
osType  | 磁碟中包含的 OS 類型
vhd     | 虛擬硬碟
writeAcceleratorEnabled | 是否在磁碟上啟用 writeAccelerator

下列範例顯示如何查詢 VM 的儲存體資訊。

**要求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
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

## <a name="vm-tags"></a>VM 標籤

VM 標籤包含執行個體/計算/標籤端點下的執行個體 API。
標籤可能已套用至您的 Azure VM，以邏輯方式將其組織成分類法。 您可以使用下列要求來擷取指派給 VM 的標籤。

**要求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**回應**

```text
Department:IT;Environment:Test;Role:WebRole
```

`tags` 欄位是字串，其中包含以分號分隔的標記。 如果標記本身使用分號，此輸出可能會造成問題。 如果將剖析器撰寫成以程式設計方式解壓縮標記，您應該依賴 `tagsList` 欄位。 此 `tagsList` 欄位是沒有分隔符號的 JSON 陣列，因此更容易剖析。

**要求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04"
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

Instance Metadata Service 所提供的部分案例，是為了保證所提供的資料確實來自 Azure。 我們會簽署這項資訊的其中一部分，從而確定市集映像就是其在 Azure 上執行的映像。

### <a name="sample-1-getting-attested-data"></a>範例 1：取得證明資料

> [!NOTE]
> 所有 API 回應都是 JSON 字串。 下列範例回應均美化顯示，很容易閱讀。

**要求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

Api-version 是必要的欄位。 如需支援的 API 版本，請參閱[使用方式一節](#usage)。
Nonce 是選用的 10 位數字串。 如果未提供，IMDS 會在其位置傳回目前的 UTC 時間戳記。

> [!NOTE]
> 由於 IMDS 的快取機制，可能會傳回先前快取的 nonce 值。

**回應**

> [!NOTE]
> 回應是 JSON 字串。 下列範例回應均列印清晰，很容易閱讀。

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

簽章 Blob 是以 [pkcs7](https://aka.ms/pkcs7) 簽署的文件版本。 它包含用於簽署的憑證，以及特定 VM 特定的詳細資料。 針對 ARM Vm，這包括 vmId、sku、nonce、subscriptionId、檔的建立和到期時間戳記，以及影像的方案資訊。 Azure Marketplace 映像才會填入方案資訊。 若是傳統 (非 ARM) Vm，則只保證會填入 vmId。 憑證可以從回應中擷取出來，並可用來驗證回應有效且來自 Azure。
檔包含下欄欄位：

資料 | 描述
-----|------------
nonce | 可選擇性隨要求提供的字串。 如果未提供任何 nonce，則會使用目前的 UTC 時間戳記
計劃 | [Azure Marketplace 的影像方案](/rest/api/compute/virtualmachines/createorupdate#plan)。 包含方案識別碼 (名稱) 、產品影像或供應專案 (產品) ，以及發行者識別碼 (發行者) 。
timestamp/createdOn | 建立簽署的檔時的 UTC 時間戳記
timestamp/expiresOn | 簽署的檔到期時的 UTC 時間戳記
vmId |  VM 的[唯一識別碼](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)
subscriptionId | 虛擬機器的 Azure 訂用帳戶，於 `2019-04-30` 引進
sku | VM 映像的特定 SKU，於 `2019-11-01` 引進

> [!NOTE]
> 若是傳統 (非 ARM) Vm，則只保證會填入 vmId。

### <a name="sample-2-validating-that-the-vm-is-running-in-azure"></a>範例 2：驗證 VM 是在 Azure 中執行

Marketplace 廠商想要確保其軟體是授權為只在 Azure 中執行。 如果有人將 VHD 複製到內部部署環境，這些廠商必須有能力偵測到此情況。 藉由呼叫 Instance Metadata Service，Marketplace 廠商可以取得簽署的資料，以保證回應只會來自 Azure。

> [!NOTE]
> 必須安裝 jq。

**要求**

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2019-04-30" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**回應**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

確認簽章是來自 Microsoft Azure，並檢查憑證鏈是否有錯誤。

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

> [!NOTE]
> 由於 IMDS 的快取機制，可能會傳回先前快取的 nonce 值。

如果您在初始要求中提供 nonce 參數，則可以比較已簽署檔中的 nonce。

> [!NOTE]
> 公用雲端和主權雲端的憑證將會不同。

Cloud | 憑證
------|------------
[所有正式推出的全域 Azure 區域](https://azure.microsoft.com/regions/) | *.metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)          | *.metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)     | *.metadata.azure.cn
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                | *.metadata.microsoftazure.de

> [!NOTE]
> 用於簽署的憑證發生已知問題。 憑證可能沒有完全符合公用雲端的 `metadata.azure.com`。 因此，認證驗證應該允許來自任何 `.metadata.azure.com` 子域的一般名稱。

在驗證期間由於網路限制而無法下載中繼憑證的情況下，可以釘選中繼憑證。 不過，Azure 會根據標準 PKI 作法來變換憑證。 釘選的憑證必須在變換發生時更新。 每當規劃更新中繼憑證的變更時，就會更新 Azure 部落格，並會通知 Azure 客戶。 您可以在[這裡](https://www.microsoft.com/pki/mscorp/cps/default.htm)找到中繼憑證。 每個區域的中繼憑證可能不同。

> [!NOTE]
> 適用於 Azure China 21Vianet 的中繼憑證將來自 DigiCert Global Root CA，而不是巴爾的摩。
此外，如果您已將 Azure 中國的中繼憑證釘選為根鏈結授權單位變更的一部分，則必須更新中繼憑證。

## <a name="managed-identity-via-metadata-service"></a>透過 Metadata Service 的受控識別

您可以在 VM 上啟用系統指派的受控識別，或可將一或多個使用者指派的受控識別指派給 VM。
接著，您可以從 Instance Metadata Service 要求受控識別的權杖。 這些權杖可以用來向其他 Azure 服務（例如 Azure Key Vault）進行驗證。

如需啟用這項功能的詳細步驟，請參閱[取得存取權杖](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)。

## <a name="scheduled-events-via-metadata-service"></a>透過中繼資料服務的已排定事件
您可以透過中繼資料服務取得已排定事件的狀態，然後使用者可以指定要在這些事件上執行的一組動作。  如需詳細資料，請參閱[已排定事件](scheduled-events.md)。 

## <a name="regional-availability"></a>區域可用性

服務已在所有 Azure 雲端中 **正式推出** 。

## <a name="sample-code-in-different-languages"></a>不同語言的程式碼範例

在 VM 內使用不同語言呼叫中繼資料服務的範例：

Language      | 範例
--------------|----------------
Bash          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
C#            | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Go            | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Java          | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
NodeJS        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Perl          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
PowerShell    | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Puppet        | https://github.com/keirans/azuremetadata
Python        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
Ruby          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="error-and-debugging"></a>測試和偵錯

如果找不到資料元素或要求的格式錯誤，則執行個體中繼資料服務會傳回標準 HTTP 錯誤。 例如：

HTTP 狀態碼 | 原因
----------------|-------
200 確定 |
400 不正確的要求 | `Metadata: true` `format=json` 查詢分葉節點時遺漏標頭或遺漏參數
404 找不到 | 要求的元素不存在
405 不允許的方法 | 僅支援 `GET` 要求
410 不存在 | 在一段時間後重試，上限為 70 秒
429 要求太多 | API 目前支援最多每秒 5 個查詢
500 服務錯誤     | 稍後重試

### <a name="known-issues-and-faq"></a>已知問題和常見問題集

1. 我收到錯誤 `400 Bad Request, Required metadata header not specified`。 這代表什麼？
   * 執行個體中繼資料服務需要在要求中傳遞標頭 `Metadata: true`。 在 REST 呼叫中傳遞此標頭可允許存取執行個體中繼資料服務。
1. 為什麼我沒有收到我的 VM 計算資訊？
   * 目前執行個體中繼資料服務僅支援使用 Azure Resource Manager 建立的執行個體。 未來可能會新增雲端服務 VM 的支援。
1. 我在一陣子之後回過頭來透過 Azure Resource Manager 建立我的虛擬機器。 為什麼我看不到計算中繼資料資訊？
   * 針對在 2016 年 9 月之後建立的 VM，新增[標記](../../azure-resource-manager/management/tag-resources.md)才會開始看到計算中繼資料。 針對 (在 Sep 2016) 之前建立的舊版 Vm，請將擴充功能或資料磁片新增/移除至 VM 實例 (s) 以重新整理中繼資料。
1. 我看不到為新版本填入的所有資料
   * 針對在 2016 年 9 月之後建立的 VM，新增[標記](../../azure-resource-manager/management/tag-resources.md)才會開始看到計算中繼資料。 針對 (在 Sep 2016) 之前建立的舊版 Vm，請將擴充功能或資料磁片新增/移除至 VM 實例 (s) 以重新整理中繼資料。
1. 為什麼會收到錯誤 `500 Internal Server Error` 或 `410 Resource Gone` ？
   * 請根據指數後置系統或 [暫時性錯誤處理](/azure/architecture/best-practices/transient-faults)中所述的其他方法，重試您的要求。 如果問題持續發生，請在 VM 的 Azure 入口網站中建立支援問題。
1. 這是否適用于虛擬機器擴展集實例？
   * Yes Metadata service 適用于擴展集實例。
1. 我已更新虛擬機器擴展集中的標籤，但它們不會出現在實例中，與單一實例 Vm 不同嗎？
   * 擴展集目前的標籤只會在重新開機、重新安裝映射或磁片變更至實例時向 VM 顯示。
1. 為何在呼叫服務時會出現要求逾時的狀況？
   * 中繼資料呼叫必須從指派給 VM 主要網路卡的主要 IP 位址進行。 此外，如果您已變更路由，則在 VM 的本機路由表中必須有 169.254.169.254/32 位址的路由。
   * <details>
        <summary>驗證您的路由表</summary>

        1. 使用命令（例如）傾印本機路由表 `netstat -r` ，並尋找 IMDS 專案 (例如 ) ：
            ```console
            ~$ netstat -r
            Kernel IP routing table
            Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
            default         _gateway        0.0.0.0         UG        0 0          0 eth0
            168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
            169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
            172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
            ```
        1. 確認有路由存在 `169.254.169.254` ，並記下對應的網路介面 (例如 `eth0`) 。
        1. 傾印路由表中對應介面的介面設定 (請注意設定檔的確切名稱可能會有所不同) 
            ```console
            ~$ cat /etc/netplan/50-cloud-init.yaml
            network:
            ethernets:
                eth0:
                    dhcp4: true
                    dhcp4-overrides:
                        route-metric: 100
                    dhcp6: false
                    match:
                        macaddress: 00:0d:3a:e4:c7:2e
                    set-name: eth0
            version: 2
            ```
        1. 如果您使用動態 IP，請注意 MAC 位址。 如果您使用靜態 IP，您可能會注意到列出的 IP (s) 和/或 MAC 位址。
        1. 確認介面對應至 VM 的主要 NIC 和主要 IP。 您可以藉由查看 Azure 入口網站中的網路設定，或查閱 [Azure CLI](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-show)來尋找主要 NIC/IP。 請注意公用和私人 Ip (以及 MAC 位址（如果使用 cli) ）。 PowerShell CLI 範例：
            ```powershell
            $ResourceGroup = '<Resource_Group>'
            $VmName = '<VM_Name>'
            $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
            foreach($NicName in $NicNames)
            {
                $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
                Write-Host $NicName, $Nic.primary, $Nic.macAddress
            }
            # Output: ipexample606 True 00-0D-3A-E4-C7-2E
            ```
        1. 如果不相符，請更新路由表，讓主要 NIC/IP 成為目標。
    </details>

## <a name="support-and-feedback"></a>支援與意見反應

提交您的意見反應和意見 https://feedback.azure.com 。

若要取得服務支援，請在 Azure 入口網站中針對您無法在長時間重試後取得中繼資料回應的 VM 建立支援問題。
使用的 [問題類型] `Management` ，然後選取 `Instance Metadata Service` 做為類別目錄。

![執行個體中繼資料支援](./media/instance-metadata-service/InstanceMetadata-support.png "螢幕擷取畫面：當 Instance Metadata Service 遇到問題時開啟支援案例")

## <a name="next-steps"></a>後續步驟

深入了解：
1. [取得 VM 的 MSI 存取權杖](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)。
1. [已排定事件](scheduled-events.md)