---
title: 創建塊 Blob 存儲帳戶 - Azure 存儲 |微軟文檔
description: 演示如何創建具有高級性能特徵的 Azure BlockBlob 存儲帳戶。
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6303644ada5c6f093611dba94daf8006f8cc5819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536899"
---
# <a name="create-a-blockblobstorage-account"></a>創建塊 Blob 存儲帳戶

BlockBlob 存儲帳戶類型允許您創建具有高級性能特徵的塊 Blob。 這種類型的存儲帳戶針對事務速率高或存取時間非常快的工作負載進行了優化。 本文演示如何使用 Azure 門戶、Azure CLI 或 Azure PowerShell 創建塊 Blob 存儲帳戶。

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

有關塊 Blob 存儲帳戶的詳細資訊，請參閱[Azure 存儲帳戶概述](https://docs.microsoft.com/azure/storage/common/storage-account-overview)。

## <a name="prerequisites"></a>Prerequisites

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

無。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

此"執行"文章需要 Azure PowerShell 模組 Az 版本 1.2.0 或更高版本。 執行 `Get-Module -ListAvailable Az` 來尋找您目前的版本。 如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

您可以登入 Azure，並且以下列兩種方式之一執行 Azure CLI 命令：

- 您可以在 Azure 雲外殼中從 Azure 門戶中運行 CLI 命令。
- 您可以安裝 CLI 並在本地運行 CLI 命令。

### <a name="use-azure-cloud-shell"></a>使用 Azure Cloud Shell

Azure Cloud Shell 是免費的 Bash Shell，您可以直接在 Azure 入口網站內執行。 Azure CLI 已預先安裝並配置為與您的帳戶一起使用。 按一下 Azure 門戶右上角功能表中的 **"雲殼"** 按鈕：

[![Cloud Shell](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

該按鈕將啟動一個互動式外殼，可用於運行本操作操作文章中概述的步驟：

[![顯示門戶中的雲殼視窗的螢幕截圖](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>在本機安裝 CLI

您也可以在本機安裝及使用 Azure CLI。 此"執行"文章要求您運行 Azure CLI 版本 2.0.46 或更高版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 

---

## <a name="sign-in-to-azure"></a>登入 Azure

# <a name="portal"></a>[入口網站](#tab/azure-portal)

登錄到 Azure[門戶](https://portal.azure.com)。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

使用 `Connect-AzAccount` 命令登入 Azure 訂用帳戶，並遵循畫面上的指示進行驗證。

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要啟動 Azure 雲外殼，請登錄到[Azure 門戶](https://portal.azure.com)。

要登錄到 CLI 的本地安裝，請運行[az 登錄](/cli/azure/reference-index#az-login)命令：

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>創建塊 Blob 存儲帳戶

## <a name="portal"></a>[入口網站](#tab/azure-portal)
要在 Azure 門戶中創建塊 Blob 存儲帳戶，請按照以下步驟操作：

1. 在 Azure 門戶中，選擇存儲類別**Storage**>**存儲帳戶**>**所有服務**。

1. 在 **"存儲帳戶**"下，選擇 **"添加**"。

1. 在 **"訂閱"** 欄位中，選擇要在其中創建存儲帳戶的訂閱。

1. 在 **"資源組"** 欄位中，選擇現有資源組或選擇 **"創建新**"，然後輸入新資源組的名稱。

1. 在 **"存儲帳戶名稱**"欄位中，輸入帳戶的名稱。 請注意以下準則：

   - 此名稱在整個 Azure 中必須是唯一的。
   - 名稱必須長在 3 到 24 個字元之間。
   - 名稱只能包括數位和小寫字母。

1. 在 **"位置"** 欄位中，為存儲帳戶選擇位置或使用預設位置。

1. 對於其餘設置，請配置以下內容：

   |欄位     |值  |
   |---------|---------|
   |**效能**    |  選擇 **"高級**"。   |
   |**帳戶類型**    | 選擇**塊 Blob 存儲**。      |
   |**複製**    |  保留**本地冗余存儲 （LRS） 的**預設設置。      |

   ![顯示門戶 UI 以創建塊 Blob 存儲帳戶](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. 選擇 **"查看 + 創建**"以查看存儲帳戶設置。

1. 選取 [建立]****。

## <a name="azure-powershell"></a>[Azure 電源外殼](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. 打開提升的 Windows PowerShell 會話（以管理員身份運行）。

1. 運行以下命令以確保安裝了`Az`最新版本的 PowerShell 模組。

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. 打開新的 PowerShell 主控台，然後使用 Azure 帳戶登錄。

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. 如果需要，請創建新的資源組。 替換引號中的值，並運行以下命令。

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. 創建塊 Blob 存儲帳戶。 替換引號中的值，並運行以下命令。

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要使用 Azure CLI 創建塊 Blob 帳戶，必須首先安裝 Azure CLI v。 2.0.46 或更高版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

1. 登入您的 Azure 訂用帳戶。

   ```azurecli
   az login
   ```

1. 如果需要，請創建新的資源組。 替換括弧中的值（包括括弧），並運行以下命令。

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. 創建塊 Blob 存儲帳戶。 替換括弧中的值（包括括弧），並運行以下命令。

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

---

## <a name="next-steps"></a>後續步驟

- 如需有關儲存體帳戶的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](https://docs.microsoft.com/azure/storage/common/storage-account-overview)。

- 有關資源組的詳細資訊，請參閱[Azure 資源管理器概述](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。
