---
author: LittleBoxOfSunshine
manager: KumariSupriya
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: include
ms.date: 01/04/2021
ms.author: chhenk
ms.reviewer: azmetadatadev
ms.custom: references_regions
ms.openlocfilehash: ceb560a3f0c56a13b9f8da6c867f513b2b08e59b
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97962194"
---
# <a name="azure-instance-metadata-service-imds"></a>Azure Instance Metadata Service (IMDS) 

Azure Instance Metadata Service (IMDS) 提供目前執行中虛擬機器實例的相關資訊。 您可以使用它來管理和設定您的虛擬機器。
此資訊包括 SKU、儲存體、網路設定和即將進行的維護事件。 如需可用資料的完整清單，請參閱 [端點類別摘要](#endpoint-categories)。

IMDS 可用於執行 (Vm) 和虛擬機器擴展集實例的虛擬機器實例。 所有端點都支援使用 [Azure Resource Manager](/rest/api/resources/)建立和管理的 vm。 只有實例類別目錄的證明類別和網路部分，才支援使用傳統部署模型建立的 Vm。 證明端點只會在有限範圍內執行。

IMDS 是 () 的知名、無法路由傳送的 IP 位址所提供的 REST API `169.254.169.254` 。 您只能從 VM 內進行存取。 VM 與 IMDS 之間的通訊絕不會離開主機。
當查詢 IMDS 時，您的 HTTP 用戶端會略過 VM 內的 web proxy，並且 `169.254.169.254` 將與相同 [`168.63.129.16`](../articles/virtual-network/what-is-ip-address-168-63-129-16.md) 。

## <a name="usage"></a>使用方式

### <a name="access-azure-instance-metadata-service"></a>存取 Azure Instance Metadata Service

若要存取 IMDS，請從 [Azure Resource Manager](/rest/api/resources/) 或 [Azure 入口網站](https://portal.azure.com)建立 VM，並使用下列範例。
如需更多範例，請參閱 [Azure 實例中繼資料範例](https://github.com/microsoft/azureimds)。

以下是用來取得實例之所有中繼資料的範例程式碼。 若要存取特定的資料來源，請參閱 [端點類別](#endpoint-categories) 以取得所有可用功能的總覽。

**要求**

> [!IMPORTANT]
> 此範例會略過 proxy。 查詢 IMDS 時，您 **必須** 略過 proxy。 如 [需其他資訊，請](#proxies) 參閱 proxy。

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01"
```

---

**回應**

> [!NOTE]
> 回應是 JSON 字串。 下列範例回應均列印清晰，很容易閱讀。

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

## <a name="security-and-authentication"></a>安全性和驗證

Instance Metadata Service 只能在無法路由傳送的 IP 位址上，從執行中的虛擬機器實例內進行存取。 Vm 只能與本身相關的中繼資料/功能互動。 API 僅限 HTTP，而且永遠不會離開主機。

為了確保要求直接適用于 IMDS，並防止非預期或不必要的要求重新導向，要求：
- **必須** 包含標頭 `Metadata: true`
- 不得 **包含** `X-Forwarded-For` 標頭

服務將會拒絕任何不符合這 **兩項** 需求的要求。

> [!IMPORTANT]
> IMDS **不** 是敏感性資料的通道。 API 未經過驗證，且會開放給 VM 上的所有進程。 透過此服務公開的資訊，應視為 VM 內所有執行中應用程式的共用資訊。

## <a name="proxies"></a>Proxy

IMDS **不** 適合在 proxy 後方使用，因此不支援此做法。 大部分的 HTTP 用戶端都會提供選項讓您停用要求的 proxy，而且必須在與 IMDS 通訊時使用此功能。 如需詳細資訊，請參閱用戶端的檔。

> [!IMPORTANT]
> 即使您不知道環境中的任何 proxy **設定，還是必須覆寫任何預設用戶端 proxy 設定**。 您可以自動探索 Proxy 設定，如果無法略過這類設定，就會讓您在未來電腦的設定變更時，outrage 風險。

## <a name="rate-limiting"></a>速率限制

一般而言，IMDS 的要求限制為每秒5個要求。 超過此閾值的要求將會遭到拒絕，並出現429回應。 [受控識別](#managed-identity)分類的要求限制為每秒20個要求和5個並行要求。

## <a name="http-verbs"></a>HTTP 動詞

目前支援下列 HTTP 動詞命令：

| 動詞命令 | 描述 |
|------|-------------|
| `GET` | 取出要求的資源

## <a name="parameters"></a>參數

端點可能支援必要和/或選用參數。 如需詳細資訊，請參閱相關特定端點的 [架構](#schema) 和檔。

### <a name="query-parameters"></a>查詢參數

IMDS 端點支援 HTTP 查詢字串參數。 例如： 

```
http://169.254.169.254/metadata/instance/compute?api-version=2019-06-04&format=json
```

指定參數：

| 名稱 | 值 |
|------|-------|
| `api-version` | `2019-06-04`
| `format` | `json`

具有重複查詢參數名稱的要求將會遭到拒絕。

### <a name="route-parameters"></a>路由參數

針對傳回較大 json blob 的某些端點，我們支援將路由參數附加至要求端點，以篩選出回應的子集： 

```
http://169.254.169.254/metadata/<endpoint>/[<filter parameter>/...]?<query parameters>
```
參數會對應至用來向下切入 json 物件的索引/索引鍵，與已剖析的標記法互動。

例如，會傳回 `/metatadata/instance` json 物件：
```json
{
    "compute": { ... },
    "network": {
        "interface": [
            {
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
            },
            ...
        ]
    }
}
```

如果我們想要將回應篩選成僅限計算屬性，我們會傳送要求： 
```
http://169.254.169.254/metadata/instance/compute?api-version=<version>
```

同樣地，如果我們想要篩選到嵌套的屬性或特定陣列元素，我們會繼續附加索引鍵： 
```
http://169.254.169.254/metadata/instance/network/interface/0?api-version=<version>
```
會篩選至屬性中的第一個元素 `Network.interface` ，並傳回：

```json
{
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
}
```

> [!NOTE]
> 篩選至分葉節點時， `format=json` 無法運作。 必須明確指定這些查詢， `format=text` 因為預設格式為 json。

## <a name="schema"></a>結構描述

### <a name="data-format"></a>資料格式

根據預設，IMDS 會以 JSON 格式傳回 (`Content-Type: application/json`) 的資料。 不過，支援回應篩選的端點 (查看 [路由參數](#route-parameters)) 也支援格式 `text` 。

若要存取非預設的回應格式，請指定要求的格式作為要求中的查詢字串參數。 例如：

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

---

在 json 回應中，所有的基本專案都是類型 `string` ，而且一律會包含遺漏或不適用值，但會設定為空字串。

### <a name="versioning"></a>版本控制

IMDS 已建立版本，並在 HTTP 要求中指定 API 版本是必要的。 這項需求的唯一例外狀況是 [版本](#versions) 端點，可用來動態取出可用的 API 版本。

新增較新版本時，如果您的指令碼對於特定資料格式有相依性，則因為相容性而仍可存取較舊版本。

當您未指定版本時，您會收到包含最新支援版本清單的錯誤：
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

#### <a name="supported-api-versions"></a>支援的 API 版本
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
> 目前正在推出2020-10-01 版，而且可能尚未在每個區域中提供。

### <a name="swagger"></a>Swagger

適用于 IMDS 的完整 Swagger 定義位於： https://github.com/Azure/azure-rest-api-specs/blob/master/specification/imds/data-plane/readme.md

## <a name="regional-availability"></a>區域可用性

服務已在所有 Azure 雲端中 **正式推出** 。

## <a name="root-endpoint"></a>根端點

根端點是 `http://169.254.169.254/metadata` 。

## <a name="endpoint-categories"></a>端點分類

IMDS API 包含多個代表不同資料來源的端點類別，每個都包含一或多個端點。 請參閱每個類別以取得詳細資料。

| 類別根目錄 | 描述 | 引進的版本 |
|---------------|-------------|--------------------|
| `/metadata/attested` | 請參閱[證明資料](#attested-data) | 2018-10-01
| `/metadata/identity` | 請參閱 [受控識別 VIA IMDS](#managed-identity) | 2018-02-01
| `/metadata/instance` | 查看 [實例中繼資料](#instance-metadata) | 2017-04-02
| `/metadata/scheduledevents` | 查看 [Scheduled Events VIA IMDS](#scheduled-events) | 2017-08-01
| `/metadata/versions` | 查看 [版本](#versions) | 不適用

## <a name="versions"></a>版本

> [!NOTE]
> 這項功能與2020-10-01 版一起發行，目前正在推出，而且可能尚未在每個區域中提供。

### <a name="list-api-versions"></a>列出 API 版本

傳回支援的 API 版本集合。

```
GET /metadata/versions
```

#### <a name="parameters"></a>參數

無 (此端點未建立版本) 。

#### <a name="response"></a>回應

```json
{
  "apiVersions": [
    "2017-03-01",
    "2017-04-02",
    ...
  ]
}
```

## <a name="instance-metadata"></a>執行個體中繼資料

### <a name="get-vm-metadata"></a>取得 VM 中繼資料

公開 VM 實例的重要中繼資料，包括計算、網路和儲存體。 

```
GET /metadata/instance
```

#### <a name="parameters"></a>參數

| 名稱 | 必要/選用 | 描述 |
|------|-------------------|-------------|
| `api-version` | 必要 | 用來服務要求的版本。
| `format` | 參數 | 回應 (的格式 `json` 或 `text`) 。 * 注意：使用要求參數時可能需要

此端點可透過 [路由參數](#route-parameters)支援回應篩選。

#### <a name="response"></a>回應

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

架構細目：

**計算**

| 資料 | 描述 | 引進的版本 |
|------|-------------|--------------------|
| `azEnvironment` | VM 執行所在的 Azure 環境 | 2018-10-01
| `customData` | 這項功能目前已停用。 當此檔可用時，我們會更新此檔 | 2019-02-01
| `isHostCompatibilityLayerVm` | 識別 VM 是否在主機相容性層上執行 | 2020-06-01
| `licenseType` | [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit)的授權類型。 只有已啟用 AHB 的 Vm 才會出現此情況 | 2020-09-01
| `location` | VM 執行所在的 Azure 區域 | 2017-04-02
| `name` | VM 的名稱 | 2017-04-02
| `offer` | 提供 VM 映像的資訊，而且只針對從 Azure 映像資源庫部署的映像呈現 | 2017-04-02
| `osProfile.adminUsername` | 指定系統管理員帳戶的名稱 | 2020-07-15
| `osProfile.computerName` | 指定電腦的名稱 | 2020-07-15
| `osProfile.disablePasswordAuthentication` | 指定密碼驗證是否停用。 這僅適用于 Linux Vm | 2020-10-01
| `osType` | Linux 或 Windows | 2017-04-02
| `placementGroupId` | 虛擬機器擴展集的[放置群組](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) | 2017-08-01
| `plan` | 如果[方案](/rest/api/compute/virtualmachines/createorupdate#plan)是 Azure Marketplace 映像，則其會包含 VM 的名稱、產品及發行者 | 2018-04-02
| `platformUpdateDomain` |  VM 執行所在的[更新網域](../articles/virtual-machines/manage-availability.md) | 2017-04-02
| `platformFaultDomain` | VM 執行所在的[容錯網域](../articles/virtual-machines/manage-availability.md) | 2017-04-02
| `provider` | VM 的提供者 | 2018-10-01
| `publicKeys` | 指派給 VM 和路徑的[公開金鑰集合](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) | 2018-04-02
| `publisher` | VM 映像的發佈者 | 2017-04-02
| `resourceGroupName` | 虛擬機器的[資源群組](../articles/azure-resource-manager/management/overview.md) | 2017-08-01
| `resourceId` | 資源的[完整](/rest/api/resources/resources/getbyid) ID | 2019-03-11
| `sku` | VM 映像的特定 SKU | 2017-04-02
| `securityProfile.secureBootEnabled` | 識別 VM 上是否已啟用 UEFI 安全開機 | 2020-06-01
| `securityProfile.virtualTpmEnabled` | 識別是否在 VM 上啟用虛擬的可信賴平臺模組 (TPM)  | 2020-06-01
| `storageProfile` | 請參閱下面的儲存體設定檔 | 2019-06-01
| `subscriptionId` | 虛擬機器的 Azure 訂用帳戶 | 2017-08-01
| `tags` | 虛擬機器的[標籤](../articles/azure-resource-manager/management/tag-resources.md)  | 2017-08-01
| `tagsList` | 格式化為 JSON 陣列以方便透過程式設計剖析的標籤  | 2019-06-04
| `version` | VM 映像的版本 | 2017-04-02
| `vmId` | VM 的[唯一識別碼](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2017-04-02
| `vmScaleSetName` | 虛擬機器擴展集的[虛擬機器擴展集名稱](../articles/virtual-machine-scale-sets/overview.md) | 2017-12-01
| `vmSize` | [VM 大小](../articles/virtual-machines/sizes.md) | 2017-04-02
| `zone` | 您虛擬機器的[可用性區域](../articles/availability-zones/az-overview.md) | 2017-12-01

**儲存體設定檔**

VM 的儲存體設定檔分為三種類別：映射參考、OS 磁片和資料磁片。

映像參考物件包含下列作業系統映像的相關資訊：

| 資料 | 描述 |
|------|-------------|
| `id` | 資源識別碼
| `offer` | 平台或市集映像的供應項目
| `publisher` | 映像發行者
| `sku` | 映像 sku
| `version` | 平台或市集映像的版本

OS 磁碟物件包含有關 VM 所使用 OS 磁碟的下列資訊：

| 資料 | 描述 |
|------|-------------|
| `caching` | 快取需求
| `createOption` | 如何建立 VM 的相關資訊
| `diffDiskSettings` | 暫時性磁碟設定
| `diskSizeGB` | 磁碟的大小 (以 GB 為單位)
| `image`   | 來源使用者映像虛擬硬碟
| `lun`     | 磁碟的邏輯單元編號
| `managedDisk` | 受控磁碟參數
| `name`    | 磁碟名稱
| `vhd`     | 虛擬硬碟
| `writeAcceleratorEnabled` | 是否在磁碟上啟用 writeAccelerator

資料磁碟陣列包含附加至 VM 的資料磁碟清單。 每個資料磁碟物件都包含下列資訊：

資料 | 描述 |
-----|-------------|
| `caching` | 快取需求
| `createOption` | 如何建立 VM 的相關資訊
| `diffDiskSettings` | 暫時性磁碟設定
| `diskSizeGB` | 磁碟的大小 (以 GB 為單位)
| `encryptionSettings` | 磁碟的加密設定
| `image` | 來源使用者映像虛擬硬碟
| `managedDisk` | 受控磁碟參數
| `name` | 磁碟名稱
| `osType` | 磁碟中包含的 OS 類型
| `vhd` | 虛擬硬碟
| `writeAcceleratorEnabled` | 是否在磁碟上啟用 writeAccelerator

**Network**

| 資料 | 描述 | 引進的版本 |
|------|-------------|--------------------|
| `ipv4.privateIpAddress` | VM 的本機 IPv4 位址 | 2017-04-02
| `ipv4.publicIpAddress` | VM 的公用 IPv4 位址 | 2017-04-02
| `subnet.address` | VM 的子網路位址 | 2017-04-02
| `subnet.prefix` | 子網路首碼，範例 24 | 2017-04-02
| `ipv6.ipAddress` | VM 的本機 IPv6 位址 | 2017-04-02
| `macAddress` | VM mac 位址 | 2017-04-02

**VM 標記**

VM 標籤包含執行個體/計算/標籤端點下的執行個體 API。
標籤可能已套用至您的 Azure VM，以邏輯方式將其組織成分類法。 您可以使用下列要求來擷取指派給 VM 的標籤。

`tags` 欄位是字串，其中包含以分號分隔的標記。 如果標記本身使用分號，此輸出可能會造成問題。 如果將剖析器撰寫成以程式設計方式解壓縮標記，您應該依賴 `tagsList` 欄位。 此 `tagsList` 欄位是沒有分隔符號的 JSON 陣列，因此更容易剖析。


#### <a name="sample-1-tracking-vm-running-on-azure"></a>範例 1：追蹤在 Azure 上執行的 VM

身為服務提供者，您可能需要追蹤執行軟體的 VM 數目，或者具有需要追蹤 VM 唯一性的代理程式。 若要能夠取得 VM 的唯一識別碼，請從執行個體中繼資料服務使用 `vmId` 欄位。

**要求**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"| ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

---

**回應**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

#### <a name="sample-2-placement-of-different-data-replicas"></a>範例2：放置不同的資料複本

對於特定案例，不同資料複本的放置相當重要。 例如，透過[orchestrator](https://kubernetes.io/docs/user-guide/node-selection/)的[HDFS 複本放置](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps)或容器放置可能會要求您知道 `platformFaultDomain` 和 `platformUpdateDomain` VM 正在執行。
您也可以使用適用於執行個體的[可用性區域](../articles/availability-zones/az-overview.md)來做出這些決定。
您可以直接透過 IMDS 來查詢此資料。

**要求**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**回應**

```
0
```

#### <a name="sample-3-get-more-information-about-the-vm-during-support-case"></a>範例3：在支援案例中取得 VM 的詳細資訊

身為服務提供者，您可能會收到支援呼叫，而您想要知道更多 VM 的相關資訊。 要求客戶共用計算中繼資料可以為支援專業人員提供基本資訊，以了解 Azure 上的 VM 種類。

**要求**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

---

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

#### <a name="sample-4-get-the-azure-environment-where-the-vm-is-running"></a>範例4：取得 VM 執行所在的 Azure 環境

Azure 有各種不同的主權雲端，例如 [Azure Government](https://azure.microsoft.com/overview/clouds/government/)。 有時候您會需要 Azure 環境來進行一些執行階段決策。 下列範例會說明如何實現此行為。

**要求**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

---

**回應**

```
AzurePublicCloud
```

此處會列出雲端和 Azure 環境的值。

| Cloud | Azure 環境 |
|-------|-------------------|
| [所有正式推出的全球 Azure 區域](https://azure.microsoft.com/regions/) | AzurePublicCloud
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | AzureUSGovernmentCloud
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | AzureChinaCloud
| [Azure Germany](https://azure.microsoft.com/overview/clouds/germany/) | AzureGermanCloud


#### <a name="sample-5-retrieve-network-information"></a>範例5：取出網路資訊

**要求**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

---

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

#### <a name="sample-6-retrieve-public-ip-address"></a>範例6：取出公用 IP 位址

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

---

## <a name="attested-data"></a>證明資料

### <a name="get-attested-data"></a>取得證明資料

IMDS 可協助確保提供的資料來自 Azure。 Microsoft 會簽署這項資訊的一部分，因此您可以確認 Azure Marketplace 中的映射是您正在 Azure 上執行的映射。

```
GET /metadata/attested/document
```

#### <a name="parameters"></a>參數

| 名稱 | 必要/選用 | 描述 |
|------|-------------------|-------------|
| `api-version` | 必要 | 用來服務要求的版本。
| `nonce` | 選擇性 | 作為密碼編譯 nonce 的10位數位符串。 如果未提供任何值，則 IMDS 會使用目前的 UTC 時間戳記。

#### <a name="response"></a>回應

```json
{
    "encoding":"pkcs7",
    "signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> [!NOTE]
> 由於 IMDS 的快取機制，可能會傳回先前快取的 nonce 值。

簽名 blob 是檔的 [pkcs7](https://aka.ms/pkcs7)簽署版本。 它包含用於簽署的憑證，以及特定 VM 特定的詳細資料。

針對使用 Azure Resource Manager 建立的 vm，檔會包含 `vmId` 、 `sku` 、 `nonce` 、 `subscriptionId` 、 `timeStamp` 適用于檔的建立和到期，以及影像的計畫資訊。 Azure Marketplace 映像才會填入方案資訊。

針對使用傳統部署模型所建立的 Vm，只 `vmId` 保證會填入。 您可以從回應中解壓縮憑證，並使用它來確認回應有效且來自 Azure。

已解碼的檔包含下欄欄位：

| 資料 | 描述 | 引進的版本 |
|------|-------------|--------------------|
| `licenseType` | [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit)的授權類型。 只有已啟用 AHB 的 Vm 才會出現此情況。 | 2020-09-01
| `nonce` | 可選擇性隨要求提供的字串。 如果未 `nonce` 提供，則會使用目前的國際標準時間時間戳記。 | 2018-10-01
| `plan` | [Azure Marketplace 的影像方案](/rest/api/compute/virtualmachines/createorupdate#plan)。 包含方案識別碼 (名稱) 、產品影像或供應專案 (產品) ，以及發行者識別碼 (發行者) 。 | 2018-10-01
| `timestamp.createdOn` | 建立簽署的檔時的 UTC 時間戳記 | 2018-20-01
| `timestamp.expiresOn` | 簽署的檔到期時的 UTC 時間戳記 | 2018-10-01
| `vmId` | VM 的[唯一識別碼](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2018-10-01
| `subscriptionId` | 虛擬機器的 Azure 訂用帳戶 | 2019-04-30
| `sku` | VM 映像的特定 SKU | 2019-11-01

> [!NOTE]
> 若為傳統 (非 Azure Resource Manager) Vm，則只保證會填入 vmId。

範例文件︰
```json
{
   "nonce":"20201130-211924",
   "plan":{
      "name":"planName",
      "product":"planProduct",
      "publisher":"planPublisher"
   },
   "sku":"Windows-Server-2012-R2-Datacenter",
   "subscriptionId":"8d10da13-8125-4ba9-a717-bf7490507b3d",
   "timeStamp":{
      "createdOn":"11/30/20 21:19:19 -0000",
      "expiresOn":"11/30/20 21:19:24 -0000"
   },
   "vmId":"02aab8a4-74ef-476e-8182-f6d2ba4166a6"
}
```

#### <a name="sample-1-validate-that-the-vm-is-running-in-azure"></a>範例1：驗證 VM 是否正在 Azure 中執行

Azure Marketplace 中的廠商希望確保其軟體僅能在 Azure 中執行。 如果有人將 VHD 複製到內部部署環境，則廠商必須能夠偵測到此情況。 透過 IMDS，這些廠商可以取得簽署的資料，以保證回應只會來自 Azure。

> [!NOTE]
> 此範例需要安裝 jq 公用程式。

**驗證**

#### <a name="windows"></a>[Windows](#tab/windows/)

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

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2020-09-01" | jq -r '.["signature"]' > signature
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

**結果**

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
  "licenseType": "Windows_Client",  
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

---

> [!NOTE]
> 由於 IMDS 的快取機制，可能會傳回先前快取的 `nonce` 值。

`nonce`如果您在初始要求中提供參數，則可以比較已簽署檔中的 `nonce` 。

> [!NOTE]
> 公用雲端和每個主權雲端的憑證將會不同。

| Cloud | 憑證 |
|-------|-------------|
| [所有正式推出的全球 Azure 區域](https://azure.microsoft.com/regions/) | *.metadata.azure.com
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | *.metadata.azure.us
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | *.metadata.azure.cn
| [Azure Germany](https://azure.microsoft.com/overview/clouds/germany/) | *.metadata.microsoftazure.de

> [!NOTE]
> 憑證可能不會完全符合 `metadata.azure.com` 公用雲端。 基於這個理由，憑證驗證應該允許來自任何子域的一般名稱 `.metadata.azure.com` 。

在驗證期間由於網路限制而無法下載中繼憑證的情況下，您可以釘選中繼憑證。 Azure 會變換憑證，這是標準的 PKI 實務。 進行變換時，您必須更新釘選的憑證。 每當規劃更新中繼憑證的變更時，就會更新 Azure blog，並通知 Azure 客戶。 

您可以在 [PKI 存放庫](https://www.microsoft.com/pki/mscorp/cps/default.htm)中找到中繼憑證。 每個區域的中繼憑證可能不同。

> [!NOTE]
> Azure 中國世紀的中繼憑證將來自 DigiCert Global 根 CA，而不是巴爾的摩。
如果您將 Azure 中國的中繼憑證釘選在根鏈授權單位變更中，則必須更新中繼憑證。


## <a name="managed-identity"></a>受控識別

您可以在 VM 上啟用受控識別（由系統指派）。 您也可以將一或多個使用者指派的受控識別指派給 VM。
然後，您可以從 IMDS 要求受控識別的權杖。 使用這些權杖來向其他 Azure 服務（例如 Azure Key Vault）進行驗證。

如需啟用這項功能的詳細步驟，請參閱[取得存取權杖](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)。

## <a name="scheduled-events"></a>排定的事件
您可以使用 IMDS 取得已排程事件的狀態。 然後，使用者可以指定要在這些事件上執行的一組動作。 如需詳細資訊，請參閱 [Linux 的排程事件](../articles/virtual-machines/linux/scheduled-events.md) 或 [Windows 的排定事件](../articles/virtual-machines/windows/scheduled-events.md)。

## <a name="sample-code-in-different-languages"></a>不同語言的範例程式碼

下表列出在 VM 內使用不同語言呼叫 IMDS 的範例：

| Language | 範例 |
|----------|---------|
| Bash | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
| C# | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
| Go | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
| Java | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
| NodeJS | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
| Perl | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
| PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
| Puppet | https://github.com/keirans/azuremetadata
| Python | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
| Ruby | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="errors-and-debugging"></a>錯誤和調試

如果找不到資料元素或要求的格式錯誤，則執行個體中繼資料服務會傳回標準 HTTP 錯誤。 例如：

| HTTP 狀態碼 | 原因 |
|------------------|--------|
| `200 OK` | 要求成功。
| `400 Bad Request` | `Metadata: true` `format=json` 查詢分葉節點時遺漏標頭或遺漏參數
| `404 Not Found` | 要求的元素不存在
| `405 Method Not Allowed` | 端點上不支援 (動詞) 的 HTTP 方法。
| `410 Gone` | 在一段時間後重試，上限為 70 秒
| `429 Too Many Requests` | 已超過 API[速率限制](#rate-limiting)
| `500 Service Error` | 稍後重試

## <a name="frequently-asked-questions"></a>常見問題集

**我收到錯誤 `400 Bad Request, Required metadata header not specified` 。這代表什麼意思？**

IMDS 需要在 `Metadata: true` 要求中傳遞標頭。 在 REST 呼叫中傳遞此標頭可允許存取 IMDS。

**為什麼我沒有收到我的 VM 計算資訊？**

目前，IMDS 僅支援使用 Azure Resource Manager 所建立的實例。

**我在一段時間前 Azure Resource Manager 建立了 VM。為什麼我看不到計算中繼資料資訊？**

如果您在2016年9月之後建立 VM，請新增 [標記](../articles/azure-resource-manager/management/tag-resources.md) 以開始查看計算中繼資料。 如果您在2016年9月之前建立 VM，請在 VM 實例中新增或移除擴充功能或資料磁片，以重新整理中繼資料。

**為什麼我看不到為新版本填入的所有資料？**

如果您在2016年9月之後建立 VM，請新增 [標記](../articles/azure-resource-manager/management/tag-resources.md) 以開始查看計算中繼資料。 如果您在2016年9月之前建立 VM，請在 VM 實例中新增或移除擴充功能或資料磁片，以重新整理中繼資料。

**為什麼會收到錯誤 `500 Internal Server Error` 或 `410 Resource Gone` ？**

請重試您的要求。 如需詳細資訊，請參閱 [暫時性錯誤處理](/azure/architecture/best-practices/transient-faults)。 如果問題持續發生，請在 VM 的 Azure 入口網站中建立支援問題。

**這是否適用于虛擬機器擴展集實例？**

是，IMDS 可供虛擬機器擴展集實例使用。

**我已更新虛擬機器擴展集中的標籤，但 (與單一實例 Vm) 不會出現在實例中。我是否發生錯誤？**

虛擬機器擴展集目前的標籤只會在重新開機、重新安裝映射或磁片變更至實例時向 VM 顯示。

**為什麼我的要求會在呼叫服務時停止？**

中繼資料呼叫必須從指派給 VM 主要網路卡的主要 IP 位址進行。 此外，如果您已變更路由，則在 VM 的本機路由表中必須有 169.254.169.254/32 位址的路由。

#### <a name="windows"></a>[Windows](#tab/windows/)

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
1. 確認介面對應至 VM 的主要 NIC 和主要 IP。 您可以藉由查看 Azure 入口網站中的網路設定，或查看 Azure CLI 來尋找主要 NIC 和 IP。 如果您使用的是 CLI) ，請注意私人 Ip (和 MAC 位址。 以下是 PowerShell CLI 範例：
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

#### <a name="linux"></a>[Linux](#tab/linux/)

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
1. 確認介面對應至 VM 的主要 NIC 和主要 IP。 您可以藉由查看 Azure 入口網站中的網路設定，或查看 Azure CLI 來尋找主要 NIC 和 IP。 如果您使用的是 CLI) ，請注意私人 Ip (和 MAC 位址。 以下是 PowerShell CLI 範例：
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

---

**Windows Server 中的容錯移轉叢集**

當您使用容錯移轉叢集查詢 IMDS 時，有時需要將路由新增至路由表。 其做法如下：

1. 使用系統管理員權限開啟命令提示字元。

1. 執行下列命令，並記下網路目的地的介面位址 (`0.0.0.0`) 在 IPv4 路由表中。

```bat
route print
```

> [!NOTE]
> 下列範例輸出來自已啟用容錯移轉叢集的 Windows Server VM。 為了簡單起見，輸出只會包含 IPv4 路由表。

```
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

## <a name="support"></a>支援

如果您在多次嘗試之後無法取得中繼資料回應，您可以在 Azure 入口網站中建立支援問題。

## <a name="product-feedback"></a>產品意見反應

您可以在以下的虛擬機器 > Instance Metadata Service，為我們的使用者意見反應通道提供產品意見反應和想法： https://feedback.azure.com/forums/216843-virtual-machines?category_id=394627

## <a name="next-steps"></a>後續步驟

[取得 VM 的存取權杖](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

[Linux 的排程事件](../articles/virtual-machines/linux/scheduled-events.md)

[Windows 的排程事件](../articles/virtual-machines/windows/scheduled-events.md)
