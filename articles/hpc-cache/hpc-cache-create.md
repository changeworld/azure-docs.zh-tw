---
title: 建立 Azure HPC Cache
description: 如何建立 Azure HPC Cache 執行個體
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: bed158fb99654bd48184073b1266ae630255558b
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613164"
---
# <a name="create-an-azure-hpc-cache"></a>建立 Azure HPC Cache

使用 Azure 入口網站或 Azure CLI 來建立您的快取。

![Azure 入口網站中快取概觀的螢幕擷取畫面，其中 [建立] 按鈕位於底部](media/hpc-cache-home-page.png)

按一下下圖以觀看建立快取和新增儲存體目標的 [影片示範](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) 。

[![影片縮圖： Azure HPC Cache：設定 (按一下流覽影片頁面) ](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="portal"></a>[入口網站](#tab/azure-portal)

## <a name="define-basic-details"></a>定義基本詳細資料

![Azure 入口網站中專案詳細資料頁面的螢幕擷取畫面](media/hpc-cache-create-basics.png)

在 [專案詳細資料]**** 中，選取將要裝載快取的訂用帳戶和資源群組。

在 [服務詳細資料]**** 中，設定快取名稱和其他屬性：

* 位置 - 選取其中一個[支援的區域](hpc-cache-overview.md#region-availability)。
* 虛擬網路 - 您可以選取現有的虛擬網路，或建立一個新的虛擬網路。
* 子網-選擇或建立至少有 64 IP 位址 (/24) 的子網。 此子網必須僅用於此 Azure HPC Cache 實例。

## <a name="set-cache-capacity"></a>設定快取容量
<!-- referenced from GUI - update aka.ms link if you change this header text -->

在 [快取]**** 頁面上，您必須設定快取的容量。 此處設定的值會決定您的快取可以保存多少資料，以及它服務用戶端要求的速度。

容量也會影響快取的成本。

藉由設定這兩個值來選擇容量：

* 快取 (輸送量) 的資料傳輸率上限 (以 GB/秒為單位)
* 配置給快取資料的儲存體數量 (以 TB 為單位)

選擇其中一個可用的輸送量值和快取儲存體大小。

請記住，實際的資料傳輸速率取決於工作負載、網路速度，以及儲存體目標的類型。 您選擇的值會設定整個快取系統的最大輸送量，但其中有部分會用於額外負荷工作。 例如，如果用戶端要求的檔案尚未儲存在快取中，或檔案標示為過時，您的快取會使用它的一些輸送量從後端儲存體提取它。

Azure HPC Cache 會管理要快取和預先載入哪些檔案，以最大化快取命中率。 快取內容會持續進行評估，並在較不常存取的情況下，將檔案移至長期儲存。 選擇可輕鬆保存使用中檔案集的快取儲存體大小，再加上中繼資料和其他額外負荷的額外空間。

![快取大小頁面的螢幕擷取畫面](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>啟用 Azure Key Vault 加密 (選擇性) 

如果您的快取位於支援客戶管理的加密金鑰的區域，[**磁片加密金鑰** **] 頁面**會出現**在 [** 快取] 和 [標籤] 索引標籤之間。 若要深入瞭解區域支援，請參閱 [區域可用性](hpc-cache-overview.md#region-availability) 。

如果您想要管理用於快取儲存體的加密金鑰，請在 [ **磁片加密金鑰** ] 頁面上提供您的 Azure Key Vault 資訊。 金鑰保存庫必須位於相同的區域，且與快取位於相同的訂用帳戶中。

如果您不需要客戶管理的金鑰，可以略過本節。 Azure 預設會使用 Microsoft 管理的金鑰來加密資料。 若要深入瞭解，請參閱 [Azure 儲存體加密](../storage/common/storage-service-encryption.md) 。

> [!NOTE]
>
> * 建立快取之後，您就無法在 Microsoft 管理的金鑰與客戶管理的金鑰之間變更。
> * 建立快取之後，您必須授權它才能存取金鑰保存庫。 在快取的 **[總覽**] 頁面中，按一下 [**啟用加密**] 按鈕以開啟加密。 在建立快取的90分鐘內進行此步驟。
> * 快取磁片會在此授權之後建立。 這表示初始快取建立時間很短，但在您授權存取後，快取將無法再使用10分鐘或更久。

如需客戶管理金鑰加密程式的完整說明，請參閱 [使用客戶管理的加密金鑰進行 Azure HPC Cache](customer-keys.md)。

![[加密金鑰] 頁面的螢幕擷取畫面，其中已選取 [客戶管理]，並顯示金鑰保存庫欄位](media/create-encryption.png)

選取 [ **客戶管理** ] 以選擇客戶管理的金鑰加密。 [金鑰保存庫規格] 欄位隨即出現。 選取要使用的 Azure Key Vault，然後選取要用於此快取的金鑰和版本。 金鑰必須是2048位 RSA 金鑰。 您可以從這個頁面建立新的金鑰保存庫、金鑰或金鑰版本。

建立快取之後，您必須授權它使用金鑰保存庫服務。 從快取讀取 [授權 Azure Key Vault 加密](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) 以取得詳細資料。

## <a name="add-resource-tags-optional"></a>新增資源標記 (選擇性)

[標籤]**** 頁面可讓您將[資源標籤](https://go.microsoft.com/fwlink/?linkid=873112)新增至 Azure HPC Cache 執行個體。

## <a name="finish-creating-the-cache"></a>完成建立快取

設定新的快取之後，請按一下 [ **檢查 + 建立** ] 索引標籤。入口網站會驗證您的選擇，並可讓您檢查您的選擇。 如果所有設定都正確，請按一下 [建立]****。

建立快取需要大約 10 分鐘。 您可以在 Azure 入口網站的通知面板中追蹤進度。

![入口網站中快取建立的「部署進行中」和「通知」頁面螢幕擷取畫面](media/hpc-cache-deploy-status.png)

建立完成後，會出現包含新 Azure HPC Cache 執行個體連結的通知，而快取會出現在訂用帳戶的**資源**清單中。

![Azure 入口網站中 Azure HPC Cache 執行個體的螢幕擷取畫面](media/hpc-cache-new-overview.png)

> [!NOTE]
> 如果您的快取使用客戶管理的加密金鑰，則在部署狀態變更為 [完成] 之前，快取可能會出現在 [資源] 清單中。 一旦快取的狀態為 **等待金鑰** ，您就可以 [授權它](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) 使用金鑰保存庫。

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="create-the-cache-with-azure-cli"></a>使用 Azure CLI 建立快取

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

> [!NOTE]
> Azure CLI 目前不支援使用客戶管理的加密金鑰來建立快取。 使用 Azure 入口網站。

使用 [az hpc cache create](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-create) 命令來建立新的 Azure HPC Cache。

提供這些值：

* 快取資源組名
* 快取名稱
* Azure 區域
* 快取子網，格式如下：

  ``--subnet "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"``

  快取子網需要至少64個 IP 位址 (/24) ，而且無法存放任何其他資源。

* 快取容量。 有兩個值會設定 Azure HPC Cache 的最大輸送量：

  * 快取大小 (GB) 
  * 快取基礎結構中使用的虛擬機器 SKU

  [az hpc-cache sku 清單](/cli/azure/ext/hpc-cache/hpc-cache/skus) 會顯示可用的 sku，以及每一個 sku 的有效快取大小選項。 快取大小選項的範圍是從 3 TB 到 48 TB，但只支援部分值。

  此圖表顯示 (2020 年7月) 時，哪些快取大小和 SKU 組合是有效的。

  | 快取大小 | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | 是         | 否          | 否          |
  | 6144 GB    | 是         | 是         | 否          |
  | 12288 GB   | 是         | 是         | 是         |
  | 24576 GB   | 否          | 是         | 是         |
  | 49152 GB   | 否          | 否          | 是         |

  請參閱入口網站指示索引標籤中的 [設定快取 **容量** ] 區段，以取得有關定價、輸送量和如何針對您的工作流程適當地調整快取大小的重要資訊。

快取建立範例：

```azurecli
az hpc-cache create --resource-group doc-demo-rg --name my-cache-0619 \
    --location "eastus" --cache-size-gb "3072" \
    --subnet "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default" \
    --sku-name "Standard_2G"
```

快取建立需要幾分鐘的時間。 成功時，create 命令會傳回如下的輸出：

```azurecli
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },
  "id": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619",
  "location": "eastus",
  "mountAddresses": [
    "10.3.0.17",
    "10.3.0.18",
    "10.3.0.19"
  ],
  "name": "my-cache-0619",
  "provisioningState": "Succeeded",
  "resourceGroup": "doc-demo-rg",
  "sku": {
    "name": "Standard_2G"
  },
  "subnet": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default",
  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.42",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-04-01T15:19:54.068299+00:00",
    "pendingFirmwareVersion": null
  }
}
```

此訊息包含一些實用的資訊，包括下列專案：

* 用戶端掛接位址：當您準備好要將用戶端連線至快取時，請使用這些 IP 位址。 若要深入瞭解，請參閱 [裝載 Azure HPC Cache](hpc-cache-mount.md) 。
* 升級狀態-發行軟體更新時，此訊息將會變更。 您可以在方便的時間手動升級快取 [軟體](hpc-cache-manage.md#upgrade-cache-software) ，或者會在數天后自動套用。

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

> [!CAUTION]
> Az. HPCCache PowerShell 模組目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供。 不建議用於生產工作負載。 某些功能可能不受支援，或可能具有受限的功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="requirements"></a>需求

如果您選擇在本機使用 PowerShell，本文會要求您安裝 Az PowerShell 模組，並使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) Cmdlet 連線到您的 Azure 帳戶。 如需安裝 Az PowerShell 模組的詳細資訊，請參閱[安裝 Azure PowerShell](/powershell/azure/install-az-ps)。 如果您選擇使用 Cloud Shell，請參閱 [Azure Cloud Shell 的總覽](https://docs.microsoft.com/azure/cloud-shell/overview) 以取得詳細資訊。

> [!IMPORTANT]
> **HPCCache** PowerShell 模組目前為預覽狀態，您必須使用指令程式個別進行安裝 `Install-Module` 。 在此 PowerShell 模組正式推出之後，它將會成為未來 Az PowerShell 模組版本的一部分，並以原生方式從 Azure Cloud Shell 內提供。

```azurepowershell-interactive
Install-Module -Name Az.HPCCache
```

## <a name="create-the-cache-with-azure-powershell"></a>使用 Azure PowerShell 建立快取

> [!NOTE]
> Azure PowerShell 目前不支援使用客戶管理的加密金鑰來建立快取。 使用 Azure 入口網站。

使用 [AzHpcCache](/powershell/module/az.hpccache/new-azhpccache) Cmdlet 來建立新的 Azure HPC Cache。

請提供下列值：

* 快取資源組名
* 快取名稱
* Azure 區域
* 快取子網，格式如下：

  `-SubnetUri "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"`

  快取子網需要至少64個 IP 位址 (/24) ，而且無法存放任何其他資源。

* 快取容量。 有兩個值會設定 Azure HPC Cache 的最大輸送量：

  * 快取大小 (GB) 
  * 快取基礎結構中使用的虛擬機器 SKU

  [AzHpcCacheSku](/powershell/module/az.hpccache/get-azhpccachesku) 會顯示可用的 sku，以及每一個 sku 的有效快取大小選項。 快取大小選項的範圍是從 3 TB 到 48 TB，但只支援部分值。

  此圖表顯示 (2020 年7月) 時，哪些快取大小和 SKU 組合是有效的。

  | 快取大小 | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | 是         | 否          | 否          |
  | 6144 GB    | 是         | 是         | 否          |
  | 12288 GB   | 是         | 是         | 是         |
  | 24576 GB   | 否          | 是         | 是         |
  | 49152 GB   | 否          | 否          | 是         |

  請參閱入口網站指示索引標籤中的 [設定快取 **容量** ] 區段，以取得有關定價、輸送量和如何針對您的工作流程適當地調整快取大小的重要資訊。

快取建立範例：

```azurepowershell-interactive
$cacheParams = @{
  ResourceGroupName = 'doc-demo-rg'
  CacheName = 'my-cache-0619'
  Location = 'eastus'
  cacheSize = '3072'
  SubnetUri = "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default"
  Sku = 'Standard_2G'
}
New-AzHpcCache @cacheParams
```

快取建立需要幾分鐘的時間。 成功時，create 命令會傳回下列輸出：

```Output
cacheSizeGb       : 3072
health            : @{state=Healthy; statusDescription=The cache is in Running state}
id                : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619
location          : eastus
mountAddresses    : {10.3.0.17, 10.3.0.18, 10.3.0.19}
name              : my-cache-0619
provisioningState : Succeeded
resourceGroup     : doc-demo-rg
sku               : @{name=Standard_2G}
subnet            : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default
tags              :
type              : Microsoft.StorageCache/caches
upgradeStatus     : @{currentFirmwareVersion=5.3.42; firmwareUpdateDeadline=1/1/0001 12:00:00 AM; firmwareUpdateStatus=unavailable; lastFirmwareUpdate=4/1/2020 10:19:54 AM; pendingFirmwareVersion=}
```

此訊息包含一些實用的資訊，包括下列專案：

* 用戶端掛接位址：當您準備好要將用戶端連線至快取時，請使用這些 IP 位址。 若要深入瞭解，請參閱 [裝載 Azure HPC Cache](hpc-cache-mount.md) 。
* 升級狀態-發行軟體更新時，此訊息會變更。 您可以在方便的時間手動升級快取 [軟體](hpc-cache-manage.md#upgrade-cache-software) ，也可以在數天后自動套用。

---

## <a name="next-steps"></a>後續步驟

當您的快取出現在 **資源清單** 之後，您就可以移至下一個步驟。

* [定義儲存體目標](hpc-cache-add-storage.md) ，以將您的快取存取權授與您的資料來源。
* 如果您使用客戶管理的加密金鑰，您需要從快取的 [總覽] 頁面 [授權 Azure Key Vault 加密](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) ，才能完成快取設定。 您必須執行此步驟，才能新增存放裝置。 如需詳細資訊， [請參閱使用客戶管理的加密金鑰](customer-keys.md) 。
