---
title: 建立區塊 blob 儲存體帳戶-Azure 儲存體 |Microsoft Docs
description: 說明如何建立具有 premium 效能特性的 Azure BlockBlobStorage 帳戶。
author: tamram
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 05/10/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6c23c034af40d7db2379f9d02357131a00482bd2
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495738"
---
# <a name="create-a-blockblobstorage-account"></a>建立 BlockBlobStorage 帳戶

BlockBlobStorage 帳戶種類可讓您建立具有 premium 效能特性的區塊 blob。 這種類型的儲存體帳戶已針對具有高交易率或需要極快速存取時間的工作負載優化。 本文說明如何使用 [Azure 入口網站]、[Azure CLI] 或 [Azure PowerShell] 來建立 BlockBlobStorage 帳戶。

> [!NOTE]
> 區塊 blob 儲存體帳戶中的階層式命名空間功能處於公開預覽狀態，適用于美國東部、美國東部2、美國中部、美國中南部、美國西部2、英國南部、加拿大中部和澳大利亞東部地區。 若要查看限制，請參閱 Azure Data Lake Storage Gen2 和[已知問題](data-lake-storage-known-issues.md)[中提供的 Blob 儲存體功能](data-lake-storage-supported-blob-storage-features.md)。 若要註冊預覽，請參閱[此表單](https://aka.ms/adlspremiumonboard)。

如需 BlockBlobStorage 帳戶的詳細資訊，請參閱[Azure 儲存體帳戶總覽](https://docs.microsoft.com/azure/storage/common/storage-account-overview)。

## <a name="prerequisites"></a>Prerequisites

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

無。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

本操作說明文章需要 Azure PowerShell 模組 Az version 1.2.0 或更新版本。 執行 `Get-Module -ListAvailable Az` 來尋找您目前的版本。 如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

您可以登入 Azure，並且以下列兩種方式之一執行 Azure CLI 命令：

- 您可以從 Azure 入口網站，在 Azure Cloud Shell 中執行 CLI 命令。
- 您可以安裝 CLI，並在本機執行 CLI 命令。

### <a name="use-azure-cloud-shell"></a>使用 Azure Cloud Shell

Azure Cloud Shell 是免費的 Bash Shell，您可以直接在 Azure 入口網站內執行。 Azure CLI 可預先安裝和設定，以便與您的帳戶搭配使用。 在 Azure 入口網站右上方區段的功能表上，按一下 [ **Cloud Shell** ] 按鈕：

[![Cloud Shell](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

按鈕會啟動互動式 shell，讓您用來執行本操作說明文章中所述的步驟：

[![顯示 Cloud Shell 視窗的螢幕擷取畫面](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>在本機安裝 CLI

您也可以在本機安裝及使用 Azure CLI。 本操作說明文章需要您執行 Azure CLI 版本2.0.46 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 

---

## <a name="sign-in-to-azure"></a>登入 Azure

# <a name="portal"></a>[入口網站](#tab/azure-portal)

登入 [Azure 入口網站](https://portal.azure.com)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 `Connect-AzAccount` 命令登入 Azure 訂用帳戶，並遵循畫面上的指示進行驗證。

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要啟動 Azure Cloud Shell，請登入[Azure 入口網站](https://portal.azure.com)。

若要登入您的本機安裝 CLI，請執行[az login](/cli/azure/reference-index#az-login)命令：

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>建立 BlockBlobStorage 帳戶

## <a name="portal"></a>[入口網站](#tab/azure-portal)
若要在 Azure 入口網站中建立 BlockBlobStorage 帳戶，請遵循下列步驟：

1. 在 [Azure 入口網站中，選取 [**所有服務**] >**儲存體**類別目錄 >**儲存體帳戶**]。

2. 在 [**儲存體帳戶**] 底下，選取 [**新增**]。

3. 在 [**訂閱**] 欄位中，選取要在其中建立儲存體帳戶的訂用帳戶。

4. 在 [**資源群組**] 欄位中，選取現有的資源群組 **，或**選取 [新建]，然後輸入新資源群組的名稱。

5. 在 [**儲存體帳戶名稱**] 欄位中，輸入帳戶的名稱。 請注意下列指導方針：

   - 此名稱在整個 Azure 中必須是唯一的。
   - 名稱的長度必須介於3到24個字元之間。
   - 名稱只能包含數位和小寫字母。

6. 在 [**位置**] 欄位中，選取儲存體帳戶的位置，或使用預設位置。

7. 針對其餘的設定，請設定下列各項：

   |欄位     |值  |
   |---------|---------|
   |**效能**    |  選取 [ **Premium**]。   |
   |**帳戶類型**    | 選取 [ **BlockBlobStorage**]。      |
   |**複寫**    |  保留 [**本機-多餘儲存體（LRS）**] 的預設設定。      |

   ![顯示用來建立區塊 blob 儲存體帳戶的入口網站 UI](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

8. 選擇 [ **Advanced** ] 索引標籤。

9. 如果您想要將儲存體帳戶優化以進行資料分析，請將**階層命名空間**設定為 [**已啟用**]。 否則，請將此選項設為預設值。

   若要深入瞭解，請參閱[Azure Data Lake Storage Gen2 簡介](data-lake-storage-introduction.md)。

   > [!NOTE]
   > 區塊 blob 儲存體帳戶中的階層式命名空間功能處於公開預覽狀態，適用于美國東部、美國東部2、美國中部、美國中南部、美國西部2、英國南部、加拿大中部和澳大利亞東部地區。 若要查看限制，請參閱 Azure Data Lake Storage Gen2 和[已知問題](data-lake-storage-known-issues.md)[中提供的 Blob 儲存體功能](data-lake-storage-supported-blob-storage-features.md)。 若要註冊預覽，請參閱[此表單](https://aka.ms/adlspremiumonboard)。

8. 選取 [**審查 + 建立**] 以查看儲存體帳戶設定。

9. 選取 [建立]。

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. 開啟提升許可權的 Windows PowerShell 會話（以系統管理員身分執行）。

2. 執行下列命令，確定已安裝最新版本的 `Az` PowerShell 模組。

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

3. 開啟新的 PowerShell 主控台，並使用您的 Azure 帳戶登入。

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

4. 如有需要，請建立新的資源群組。 取代引號中的值，然後執行下列命令。

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

5. 建立 BlockBlobStorage 帳戶。 取代引號中的值，然後執行下列命令。

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```
   如果您想要將儲存體帳戶優化以進行資料分析，請將新增 `-EnableHierarchicalNamespace $True` 至命令。 若要深入瞭解，請參閱[Azure Data Lake Storage Gen2 簡介](data-lake-storage-introduction.md)。

   > [!NOTE]
   > 區塊 blob 儲存體帳戶中的階層式命名空間功能處於公開預覽狀態，適用于美國東部、美國東部2、美國中部、美國中南部、美國西部2、英國南部、加拿大中部和澳大利亞東部地區。 若要查看限制，請參閱 Azure Data Lake Storage Gen2 和[已知問題](data-lake-storage-known-issues.md)[中提供的 Blob 儲存體功能](data-lake-storage-supported-blob-storage-features.md)。 若要註冊預覽，請參閱[此表單](https://aka.ms/adlspremiumonboard)。

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 建立區塊 blob 帳戶，您必須先安裝 Azure CLI v。 2.0.46 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

1. 登入 Azure 訂用帳戶。

   ```azurecli
   az login
   ```

2. 如有需要，請建立新的資源群組。 以方括弧（包括括弧）取代值，然後執行下列命令。

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

3. 建立 BlockBlobStorage 帳戶。 以方括弧（包括括弧）取代值，然後執行下列命令。

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

   如果您想要將儲存體帳戶優化以進行資料分析，請將新增 `--hierarchical-namespace true` 至命令。 若要深入瞭解，請參閱[Azure Data Lake Storage Gen2 簡介](data-lake-storage-introduction.md)。

   > [!NOTE]
   > 區塊 blob 儲存體帳戶中的階層式命名空間功能處於公開預覽狀態，適用于美國東部、美國東部2、美國中部、美國中南部、美國西部2、英國南部、加拿大中部和澳大利亞東部地區。 若要查看限制，請參閱 Azure Data Lake Storage Gen2 和[已知問題](data-lake-storage-known-issues.md)[中提供的 Blob 儲存體功能](data-lake-storage-supported-blob-storage-features.md)。 若要註冊預覽，請參閱[此表單](https://aka.ms/adlspremiumonboard)。
   
---

## <a name="next-steps"></a>後續步驟

- 如需有關儲存體帳戶的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](https://docs.microsoft.com/azure/storage/common/storage-account-overview)。

- 如需有關資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。
