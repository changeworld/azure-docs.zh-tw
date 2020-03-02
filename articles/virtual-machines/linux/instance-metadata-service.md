---
title: Azure 執行個體中繼資料服務
description: RESTful 介面，可取得 Linux Vm 的計算、網路和近期維護事件的相關資訊。
services: virtual-machines-linux
documentationcenter: ''
author: KumariSupriya
manager: paulmey
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: b0d4d1d13a329b0d95fcd0358f6141486b4435e5
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205002"
---
# <a name="azure-instance-metadata-service"></a>Azure 執行個體中繼資料服務

Azure Instance Metadata Service （IMDS）提供目前正在執行之虛擬機器實例的相關資訊，可用來管理和設定您的虛擬機器。
提供的資訊包括 SKU、網路設定和即將進行的維護事件。 如需可用資料的完整清單，請參閱[中繼資料 api](#metadata-apis)。

Azure 的執行個體中繼資料服務是透過 [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/)建立之所有 IaaS VM 可存取的 REST 端點。
端點可以在已知的非可路由 IP 位址 (`169.254.169.254`) 取得，該位址只能從 VM 內存取。

> [!IMPORTANT]
> 這項服務已在所有 Azure 區域中**正式推出**。  它會定期接收更新，以公開有關虛擬機器執行個體的新資訊。 此頁面會反映最新可用的[中繼資料 api](#metadata-apis) 。

## <a name="service-availability"></a>服務可用性

這項服務可於正式推出的 Azure 區域中使用。 並非所有的 API 版本都能在所有 Azure 區域使用。

區域                                        | 可用性？                                 | 支援的版本
-----------------------------------------------|-----------------------------------------------|-----------------
[所有正式推出的全域 Azure 區域](https://azure.microsoft.com/regions/)     | 正式推出 | 2017-04-02、2017-08-01、2017-12-01、2018-02-01、2018-04-02、2018-10-01、2019-02-01、2019-03-11、2019-04-30、2019-06-01、2019-06-04、2019-08-01、2019-08-15
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | 正式推出 | 2017-04-02、2017-08-01、2017-12-01、2018-02-01、2018-04-02、2018-10-01、2019-02-01、2019-03-11、2019-04-30、2019-06-01、2019-06-04、2019-08-01、2019-08-15
[Azure China 21Vianet](https://www.azure.cn/)                                            | 正式推出 | 2017-04-02、2017-08-01、2017-12-01、2018-02-01、2018-04-02、2018-10-01、2019-02-01、2019-03-11、2019-04-30、2019-06-01、2019-06-04、2019-08-01、2019-08-15
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | 正式推出 | 2017-04-02、2017-08-01、2017-12-01、2018-02-01、2018-04-02、2018-10-01、2019-02-01、2019-03-11、2019-04-30、2019-06-01、2019-06-04、2019-08-01、2019-08-15

2019-11-01 版目前已部署，而且可能無法在所有區域使用。

當有服務更新和/或新的支援版本可用時，就會更新此資料表。

若要試用執行個體中繼資料服務，請從 [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) 或 [Azure 入口網站](https://portal.azure.com)的上述區域中建立 VM，並遵循以下的範例。
如需如何查詢 IMDS 的進一步範例，請參閱[Azure 實例中繼資料範例](https://github.com/microsoft/azureimds)

## <a name="usage"></a>使用量

### <a name="versioning"></a>版本控制

Instance Metadata Service 已建立版本，並在 HTTP 要求中指定 API 版本是必要的。

您可以看到此[可用性資料表](#service-availability)中所列的最新版本。

新增較新版本時，如果您的指令碼對於特定資料格式有相依性，則因為相容性而仍可存取較舊版本。

若未指定任何版本，則會傳回錯誤，其中包含最新支援版本的清單。

> [!NOTE]
> 回應是 JSON 字串。 下列範例回應均列印清晰，很容易閱讀。

**要求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance"
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

### <a name="using-headers"></a>使用標頭

查詢中繼資料執行個體服務時，您必須提供 `Metadata: true` 標頭以免不小心重新導向要求。

### <a name="retrieving-metadata"></a>擷取中繼資料

執行個體中繼資料適用於使用 [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) 建立/管理的執行中 VM。 使用下列要求，存取虛擬機器執行個體的所有資料類別：

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE]
> 所有執行個體中繼資料查詢都會區分大小寫。

### <a name="data-output"></a>資料輸出

根據預設，執行個體中繼資料服務會以 JSON 格式傳回資料 (`Content-Type: application/json`)。 不過，不同的 API 會依照要求傳回不同格式的資料。
下表是 API 可能支援之其他資料格式的參考。

API | 預設資料格式 | 其他格式
--------|---------------------|--------------
/instance | json | text
/scheduledevents | json | 無
/attested | json | 無

若要存取非預設的回應格式，請指定要求的格式作為要求中的查詢字串參數。 例如：

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> 對於分葉節點，`format=json` 無法使用。 若為這些查詢 `format=text` 需要在預設格式為 json 時明確指定。

### <a name="security"></a>安全性

執行個體中繼資料服務端點只能從非可路由 IP 位址上的執行中虛擬機器執行個體內存取。 此外，服務會拒絕任何具有 `X-Forwarded-For` 標頭的要求。
要求也必須包含 `Metadata: true` 標頭，以確認直接預期實際要求，而不是非預期重新導向的一部分。

### <a name="error"></a>錯誤

如果找不到資料元素或要求的格式錯誤，則執行個體中繼資料服務會傳回標準 HTTP 錯誤。 例如：

HTTP 狀態碼 | 原因
----------------|-------
200 確定 |
400 不正確的要求 | 查詢分葉節點時遺漏 `Metadata: true` 標頭或遺失格式
404 找不到 | 要求的元素不存在
405 不允許的方法 | 僅支援 `GET` 要求
410 不存在 | 在一段時間後重試，最大值為70秒
429 要求太多 | API 目前支援最多每秒 5 個查詢
500 服務錯誤     | 稍後重試

### <a name="examples"></a>範例

> [!NOTE]
> 所有 API 回應都是 JSON 字串。 為了方便閱讀，下列所有範例回應都有整齊的列印。

#### <a name="retrieving-network-information"></a>擷取網路資訊

**要求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
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

#### <a name="retrieving-public-ip-address"></a>擷取公用 IP 位址

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>擷取執行個體的所有中繼資料

**要求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2019-06-01"
```

**回應**

> [!NOTE]
> 回應是 JSON 字串。 下列範例回應均列印清晰，很容易閱讀。

```json
{
  "compute": {
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
    "tags": "Department:IT;Environment:Prod;Role:WorkerRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

## <a name="metadata-apis"></a>中繼資料 Api

下列 Api 可透過中繼資料端點取得：

資料 | 描述 | 引進的版本
-----|-------------|-----------------------
attested | 請參閱[證明資料](#attested-data) | 2018-10-01
身分識別 | 適用於 Azure 資源的受控識別。 請參閱[取得存取權杖](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
instance | 請參閱[實例 API](#instance-api) | 2017-04-02
scheduledevents | 請參閱[排定的事件](scheduled-events.md) | 2017-08-01

### <a name="instance-api"></a>實例 API

下列計算類別可透過實例 API 取得：

> [!NOTE]
> 透過中繼資料端點，可以透過實例/計算來存取下列類別目錄

資料 | 描述 | 引進的版本
-----|-------------|-----------------------
Get-azenvironment | VM 執行所在的 Azure 環境 | 2018-10-01
customData | 這項功能目前已停用，我們將在此檔可用時加以更新 | 2019-02-01
location | VM 執行所在的 Azure 區域 | 2017-04-02
NAME | VM 的名稱 | 2017-04-02
供應項目 | 提供 VM 映射的資訊，而且只會針對從 Azure 映射庫部署的映射呈現 | 2017-04-02
osType | Linux 或 Windows | 2017-04-02
placementGroupId | 虛擬機器擴展集的[放置群組](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) | 2017-08-01
計劃 | 如果 VM 是 Azure Marketplace 映射，則包含其名稱、產品和發行者的[計畫](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) | 2018-04-02
platformUpdateDomain |  VM 執行所在的[更新網域](manage-availability.md) | 2017-04-02
platformFaultDomain | VM 執行所在的[容錯網域](manage-availability.md) | 2017-04-02
provider | VM 的提供者 | 2018-10-01
publicKeys | 指派給 VM 和路徑[的公用金鑰集合](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) | 2018-04-02
publisher | VM 映像的發佈者 | 2017-04-02
resourceGroupName | 虛擬機器的[資源群組](../../azure-resource-manager/management/overview.md) | 2017-08-01
resourceId | 資源的[完整](https://docs.microsoft.com/rest/api/resources/resources/getbyid)識別碼 | 2019-03-11
sku | VM 映像的特定 SKU | 2017-04-02
storageProfile | 請參閱[儲存體設定檔](#storage-profile) | 2019-06-01
subscriptionId | 虛擬機器的 Azure 訂用帳戶 | 2017-08-01
tags | 虛擬機器的[標籤](../../azure-resource-manager/management/tag-resources.md)  | 2017-08-01
tagsList | 格式化為 JSON 陣列以方便程式設計剖析的標記  | 2019-06-04
version | VM 映像的版本 | 2017-04-02
vmId | VM 的[唯一識別碼](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2017-04-02
vmScaleSetName | 虛擬機器擴展集的[虛擬機器擴展集名稱](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) | 2017-12-01
vmSize | [VM 大小](sizes.md) | 2017-04-02
區域 | 您虛擬機器的[可用性區域](../../availability-zones/az-overview.md) | 2017-12-01

您可以透過實例 API 取得下列網路類別：

> [!NOTE]
> 透過中繼資料端點，可以透過實例/網路/介面來存取下列類別

資料 | 描述 | 引進的版本
-----|-------------|-----------------------
ipv4/privateIpAddress | VM 的本機 IPv4 位址 | 2017-04-02
ipv4/publicIpAddress | VM 的公用 IPv4 位址 | 2017-04-02
subnet/address | VM 的子網路位址 | 2017-04-02
subnet/prefix | 子網路首碼，範例 24 | 2017-04-02
ipv6/ipAddress | VM 的本機 IPv6 位址 | 2017-04-02
macAddress | VM mac 位址 | 2017-04-02

## <a name="attested-data"></a>證明資料

Instance Metadata Service 所提供的部分案例，是為了確保所提供的資料來自 Azure。 我們會簽署這項資訊的其中一部分，從而確定市集映像就是其在 Azure 上執行的映像。

### <a name="example-attested-data"></a>證明資料範例

> [!NOTE]
> 所有 API 回應都是 JSON 字串。 下列範例回應均美化顯示，很容易閱讀。

 **要求**

 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

Api 版本是必要欄位。 如需支援的 API 版本，請參閱[服務可用性一節](#service-availability)。
Nonce 是選擇性的10位數位符串。 如果未提供，IMDS 會在其位置傳回目前的 UTC 時間戳記。 由於 IMDS 的快取機制，可能會傳回先前快取的 nonce 值。

 **回應**

> [!NOTE]
> 回應是 JSON 字串。 下列範例回應均列印清晰，很容易閱讀。

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

簽章 Blob 是以 [pkcs7](https://aka.ms/pkcs7) 簽署的文件版本。 它包含用於簽署的憑證，以及 VM 詳細資料，例如 vmId、sku、nonce、subscriptionId、檔建立和到期的時間戳記，以及有關影像的計畫資訊。 Azure Marketplace 映像才會填入方案資訊。 憑證可以從回應中擷取出來，並可用來驗證回應有效且來自 Azure。

## <a name="example-scenarios-for-usage"></a>使用方式的範例案例  

### <a name="tracking-vm-running-on-azure"></a>追蹤在 Azure 上執行的 VM

身為服務提供者，您可能需要追蹤執行軟體的 VM 數目，或者具有需要追蹤 VM 唯一性的代理程式。 若要能夠取得 VM 的唯一識別碼，請從執行個體中繼資料服務使用 `vmId` 欄位。

**要求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**回應**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>容器的位置，資料分割基礎容錯/更新網域

對於特定案例，不同資料複本的放置相當重要。 例如 [HDFS 複本放置](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps)，或透過 [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) 的容器放置，您需要知道 VM 執行所在的 `platformFaultDomain` 和 `platformUpdateDomain`。
您也可以使用適用於執行個體的[可用性區域](../../availability-zones/az-overview.md)來做出這些決定。
您可以直接透過執行個體中繼資料服務查詢此資料。

**要求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**回應**

```text
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>取得支援案例期間 VM 的相關詳細資訊

身為服務提供者，您可能會收到支援呼叫，而您想要知道更多 VM 的相關資訊。 要求客戶共用計算中繼資料可以為支援專業人員提供基本資訊，以了解 Azure 上的 VM 種類。

**要求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01"
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

### <a name="getting-azure-environment-where-the-vm-is-running"></a>取得 VM 執行所在的 Azure 環境

Azure 有各種不同的主權雲端，例如 [Azure Government](https://azure.microsoft.com/overview/clouds/government/)。 有時候您會需要 Azure 環境來進行一些執行階段決策。 下列範例會說明如何實現此行為。

**要求**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**回應**

```bash
AzurePublicCloud
```

以下列出雲端和 Azure 環境的值。

 Cloud   | Azure 環境
---------|-----------------
[所有正式推出的全域 Azure 區域](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

### <a name="getting-the-tags-for-the-vm"></a>取得 VM 的標記

標記可能已套用至您的 Azure VM，以邏輯方式將其組織成分類法。 您可以使用下面的要求來抓取指派給 VM 的標記。

**要求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**回應**

```text
Department:IT;Environment:Test;Role:WebRole
```

[`tags`] 欄位是包含以分號分隔之標記的字串。 如果標記本身使用分號，這可能會是個問題。 如果寫入剖析器以程式設計方式將標記解壓縮，您應該依賴 [`tagsList`] 欄位，這是不含分隔符號的 JSON 陣列，因此更容易剖析。

**要求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04&format=json"
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

### <a name="validating-that-the-vm-is-running-in-azure"></a>驗證 VM 是在 Azure 中執行

Marketplace 廠商想要確保其軟體是授權為只在 Azure 中執行。 如果有人將 VHD 複製到內部部署環境，則他們應該能夠偵測到該應用程式。 藉由呼叫 Instance Metadata Service，Marketplace 廠商可以取得簽署的資料，以保證回應只會來自 Azure。

> [!NOTE]
> 必須安裝 jq。

**要求**

 ```bash
  # Get the signature
   curl  --silent -H Metadata:True http://169.254.169.254/metadata/attested/document?api-version=2019-04-30 | jq -r '.["signature"]' > signature
  # Decode the signature
  base64 -d signature > decodedsignature
  #Get PKCS7 format
  openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
  # Get Public key out of pkc7
  openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
  #Get the intermediate certificate
  wget -q -O intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
  openssl x509 -inform der -in intermediate.cer -out intermediate.pem
  #Verify the contents
  openssl smime -verify -in sign.pk7 -inform pem -noverify
 ```

 **回應**

```json
Verification successful
{"nonce":"20181128-001617",
  "plan":
    {
     "name":"",
     "product":"",
     "publisher":""
    },
"timeStamp":
  {
    "createdOn":"11/28/18 00:16:17 -0000",
    "expiresOn":"11/28/18 06:16:17 -0000"
  },
"vmId":"d3e0e374-fda6-4649-bbc9-7f20dc379f34",
"subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
"sku": "RS3-Pro"
}
```

資料 | 描述
-----|------------
nonce | 使用者隨要求提供的選擇性字串。 如果要求中未提供 nonce，則會傳回目前的 UTC 時間戳記
計劃 | VM 在其 Azure Marketplace 映像中的[方案](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) \(英文\)，包含名稱、產品及發行者
timestamp/createdOn | 第一個簽署的檔建立所在的 UTC 時間戳記
timestamp/expiresOn | 簽署的檔到期的 UTC 時間戳記
vmId |  VM 的[唯一識別碼](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)
subscriptionId | 虛擬機器的 Azure 訂用帳戶，引進于 `2019-04-30`
sku | VM 映射的特定 SKU，`2019-11-01` 中引進

#### <a name="verifying-the-signature"></a>驗證簽章

收到上述簽章後，您可驗證簽章是來自 Microsoft 的。 此外，您也可以驗證中繼憑證和信任鏈結。 最後，您可以確認訂用帳戶識別碼是否正確。

> [!NOTE]
> 公用雲端和主權雲端的憑證將會不同。

 Cloud | 憑證
---------|-----------------
[所有正式推出的全域 Azure 區域](https://azure.microsoft.com/regions/)     | *. metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | *. metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | *. metadata.azure.cn
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | *. metadata.microsoftazure.de

用於簽署之憑證的已知問題。 憑證可能沒有完全符合公用雲端的 `metadata.azure.com`。 因此，憑證驗證應該允許來自任何 `.metadata.azure.com` 子域的一般名稱。

```bash

# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

在驗證期間因網路條件約束而無法下載中繼憑證的情況下，可以釘選中繼憑證。 不過，Azure 會依據標準 PKI 作法來變換憑證。 發生變換時，必須更新固定的憑證。 每當規劃更新中繼憑證的變更時，就會更新 Azure blog，並會通知 Azure 客戶。 您可以在[這裡](https://www.microsoft.com/pki/mscorp/cps/default.htm)找到中繼憑證。 每個區域的中繼憑證可能不同。

### <a name="storage-profile"></a>儲存體設定檔

Instance Metadata Service 可以提供與 VM 相關聯之儲存體磁片的詳細資料。 此資料可以在實例/計算/storageProfile 端點找到。

VM 的儲存體設定檔分成三個類別-映射參考、OS 磁片和資料磁片。

映射參考物件包含下列作業系統映射的相關資訊：

資料    | 描述
--------|-----------------
id      | 資源識別碼
供應項目   | 平臺或 marketplace 映射的供應專案
publisher | 映像發行者
sku     | 映射 sku
version | 平臺或 marketplace 映射的版本

OS 磁片物件包含 VM 所使用之 OS 磁片的下列資訊：

資料    | 描述
--------|-----------------
快取 | 快取需求
createOption | 如何建立 VM 的相關資訊
diffDiskSettings | 暫時磁片設定
diskSizeGB | 磁片的大小（以 GB 為單位）
image   | 來源使用者映射虛擬硬碟
lun     | 磁片的邏輯單元編號
managedDisk | 受控磁片參數
NAME    | 磁碟名稱
vhd     | 虛擬硬碟
writeAcceleratorEnabled | 是否在磁片上啟用 writeAccelerator

資料磁片陣列包含連接至 VM 的資料磁片清單。 每個資料磁片物件都包含下列資訊：

資料    | 描述
--------|-----------------
快取 | 快取需求
createOption | 如何建立 VM 的相關資訊
diffDiskSettings | 暫時磁片設定
diskSizeGB | 磁片的大小（以 GB 為單位）
encryptionSettings | 磁片的加密設定
image   | 來源使用者映射虛擬硬碟
managedDisk | 受控磁片參數
NAME    | 磁碟名稱
osType  | 磁片中包含的 OS 類型
vhd     | 虛擬硬碟
writeAcceleratorEnabled | 是否在磁片上啟用 writeAccelerator

以下是如何查詢 VM 儲存體資訊的範例。

**要求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
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

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>在 VM 內使用不同語言呼叫中繼資料服務的範例

Language | 範例
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Go  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata

## <a name="faq"></a>常見問題集

1. 我收到錯誤 `400 Bad Request, Required metadata header not specified`。 這代表什麼？
   * 執行個體中繼資料服務需要在要求中傳遞標頭 `Metadata: true`。 在 REST 呼叫中傳遞此標頭可允許存取執行個體中繼資料服務。
2. 為什麼我沒有收到我的 VM 計算資訊？
   * 目前執行個體中繼資料服務僅支援使用 Azure Resource Manager 建立的執行個體。 未來可能會新增雲端服務 VM 的支援。
3. 我在一陣子之後回過頭來透過 Azure Resource Manager 建立我的虛擬機器。 為什麼我看不到計算中繼資料資訊？
   * 針對在 2016 年 9 月之後建立的 VM，新增[標記](../../azure-resource-manager/management/tag-resources.md)才會開始看到計算中繼資料。 針對較舊的 VM (在 2016 年 9 月之前建立)，對 VM 新增/移除擴充功能或資料磁碟，以重新整理中繼資料。
4. 我看不到為新版本填入的所有資料
   * 針對在 2016 年 9 月之後建立的 VM，新增[標記](../../azure-resource-manager/management/tag-resources.md)才會開始看到計算中繼資料。 針對較舊的 VM (在 2016 年 9 月之前建立)，對 VM 新增/移除擴充功能或資料磁碟，以重新整理中繼資料。
5. 我為何收到錯誤 `500 Internal Server Error`？
   * 請根據指數型輪詢系統重試您的要求。 若問題持續發生，請連絡 Azure 支援。
6. 我要在哪裡共用其他問題/註解？
   * 在 https://feedback.azure.com 上傳送您的註解。
7. 這是否適用於虛擬機器擴展集執行個體？
   * 是，中繼資料服務適用於擴展集執行個體。
8. 如何取得服務支援？
   * 若要取得服務支援，請在 Azure 入口網站中針對您無法在長時間重試後取得中繼資料回應的 VM 建立支援問題。
9. 為何在呼叫服務時會出現要求逾時的狀況？
   * 中繼資料呼叫必須從指派給 VM 主要網路卡的主要 IP 位址進行，此外，如果您已變更路由，則您的網路卡必須有一個路由來提供 169.254.0.0/16 位址。
10. 我已更新虛擬機器擴展集內的標籤，但為何它們並未像 VM 一樣出現在執行個體中？
    * 目前，只有對執行個體重新開機、重新安裝映像或變更磁碟時，ScaleSets 標籤才會對 VM 顯示。

    ![執行個體中繼資料支援](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>後續步驟

- 深入了解[排程的事件](scheduled-events.md)
