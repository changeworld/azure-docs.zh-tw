---
title: 建立 Azure 檔案共用
titleSuffix: Azure Files
description: 如何使用 Azure 入口網站、PowerShell 或 Azure CLI 建立 Azure 檔案共用。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions
ms.openlocfilehash: 15f9387aac909c0245d25b3a208ed24444b2b343
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91329381"
---
# <a name="create-an-azure-file-share"></a>建立 Azure 檔案共用
若要建立 Azure 檔案共用，您需要回答三個有關您將如何使用它的問題：

- **您的 Azure 檔案共用有哪些效能需求？**  
    Azure 檔案儲存體提供標準的檔案共用 (包括交易優化、經常性存取和非經常性存取檔案共用) ，這些共用是裝載在以硬碟為基礎 (HDD 為基礎的) 硬體上，而高階檔案共用則是裝載在固態磁片型 (SSD 型) 硬體上。

- **您需要何種大小的檔案共用？**  
    標準檔案共用最多可以跨越 100 TiB，不過預設不會啟用此功能;如果您需要大於 5 TiB 的檔案共用，您將需要為您的儲存體帳戶啟用大型檔案共用功能。 Premium 檔案共用最多可以跨越 100 TiB，而不需要任何特殊設定，不過會布建 premium 檔案共用，而不是像標準檔案共用一樣隨用隨付。 這表示布建的檔案共用遠超過您所需的大小，將會增加儲存空間的總成本。

- **您的 Azure 檔案共用有哪些冗余需求？**  
    標準檔案共用提供本機冗余 (LRS) 、區域多餘的 (ZRS) 、異地冗余 (GRS) ，或地理區域冗余 (GZRS) 儲存體，不過，只有在本機冗余和區域多餘的檔案共用才支援大型檔案共用功能。 Premium 檔案共用不支援任何形式的地理位置冗余。

    在大部分區域中，提供儲存體帳戶和區域冗余（區域的區域）的本機冗余提供 Premium 檔案共用。 若要瞭解您的區域中目前是否有 premium 檔案共用，請參閱 Azure 的 [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=storage) 頁面。 如需支援 ZRS 之區域的詳細資訊，請參閱 [Azure 儲存體冗余](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

如需這三個選項的詳細資訊，請參閱 [規劃 Azure 檔案儲存體部署](storage-files-planning.md)。

## <a name="prerequisites"></a>必要條件
- 本文會假設您已經建立 Azure 訂用帳戶。 如果您還沒有訂用帳戶，則先建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)，再開始操作。
- 如果您想要使用 Azure PowerShell，請[安裝最新版本](https://docs.microsoft.com/powershell/azure/install-az-ps)。
- 如果您想要使用 Azure CLI，請[安裝最新版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)。

## <a name="create-a-storage-account"></a>建立儲存體帳戶
Azure 檔案共用已部署到*儲存體帳戶*，這是代表共用儲存體集區的最上層物件。 此儲存體集區可以用來部署多個檔案共用。 

Azure 支援多個儲存體帳戶類型，適用于客戶可能擁有的不同儲存體案例，但 Azure 檔案儲存體有兩種主要的儲存體帳戶類型。 您需要建立的儲存體帳戶類型，取決於您要建立標準檔案共用或 premium 檔案共用： 

- **一般用途第 2 版 (GPv2) 儲存體帳戶**：GPv2 儲存體帳戶可讓您在標準/硬碟型 (HDD 型) 的硬體上部署 Azure 檔案共用。 除了儲存 Azure 檔案共用，GPv2 儲存體帳戶還可以儲存其他儲存體資源，例如 Blob 容器、佇列或資料表。 檔案共用可以部署到優化的交易 (預設) 、經常性存取或非經常性存取層。

- **FileStorage 儲存體帳戶**：FileStorage 儲存體帳戶可讓您在進階/固態磁碟型 (SSD 型) 硬體上部署 Azure 檔案共用。 FileStorage 帳戶只能用來儲存 Azure 檔案共用；不能在 FileStorage 帳戶中部署其他儲存體資源 (Blob 容器、佇列、資料表等等)。

# <a name="portal"></a>[入口網站](#tab/azure-portal)
若要透過 Azure 入口網站建立儲存體帳戶，請從儀表板選取 [ **+ 建立資源** ]。 在產生的 Azure Marketplace 搜尋視窗中，搜尋 **儲存體帳戶** ，然後選取結果搜尋結果。 這會導致儲存體帳戶的總覽頁面;選取 [ **建立** ] 以繼續建立儲存體帳戶。

![瀏覽器中 [儲存體帳戶] [快速建立] 選項的螢幕擷取畫面](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>基本概念一節
要完成以建立儲存體帳戶的第一個區段會標示為「 **基本**」。 這包含建立儲存體帳戶所需的所有欄位。 若要建立 GPv2 儲存體帳戶，請確定 [ **效能** ] 選項按鈕設定為 [ *標準* ]，並選取 [ **帳戶類型** ] 下拉式清單，以 *StorageV2 (一般用途 v2) *。

![[效能] 選項按鈕的螢幕擷取畫面，其中已選取 [標準] 和 [帳戶類型] 的 [StorageV2]](media/storage-how-to-create-file-share/create-storage-account-1.png)

若要建立 FileStorage 儲存體帳戶，請確定 [ **效能** ] 選項按鈕設定為 [ *Premium* ]，並選取 [ **帳戶類型** ] 下拉式清單來 *FileStorage*。

![[效能] 選項按鈕的螢幕擷取畫面，其中已選取 [Premium] 並選取 [FileStorage] 的帳戶種類](media/storage-how-to-create-file-share/create-storage-account-2.png)

其他基本欄位與儲存體帳戶的選擇無關：
- **訂**用帳戶：要部署之儲存體帳戶的訂用帳戶。 
- **資源群組**：要部署之儲存體帳戶的資源群組。 您可以建立新的資源群組，或使用現有的資源群組。 資源群組是用來群組 Azure 服務的邏輯容器。 當您建立儲存體帳戶時，可以選擇建立新的資源群組，或使用現有的資源群組。
- **儲存體帳戶名稱**：要建立之儲存體帳戶資源的名稱。 這個名稱必須是全域唯一的，否則就可以是您想要的任何名稱。 當您透過 SMB 掛接 Azure 檔案共用時，儲存體帳戶名稱將會用來做為伺服器名稱。
- **位置**：要部署儲存體帳戶的區域。 這可以是與資源群組相關聯的區域，或任何其他可用的區域。
- 複寫 **：雖然**這是標示為 [複寫]，但這個欄位實際上表示**冗余**;這是所需的冗余層級：本機冗余 (LRS) 、區域冗余 (ZRS) 、異地複寫 (GRS) 和異地區域冗余。 此下拉式清單也包含「讀取權限異地複寫」 (GRS) 和「讀取權限地理區域冗余」 (不會套用至 Azure 檔案共用的 GZRS) ;任何在儲存體帳戶中建立且已選取的檔案共用，將會分別是地理區域冗余或地理區域冗余。 視您的區域或選取的儲存體帳戶類型而定，可能不允許某些多餘的選項。
- **Blob 存取層**：此欄位不適用於 Azure 檔案儲存體，因此您可以選擇其中一個選項按鈕。 

> [!Important]  
> 選取 blob 存取層不會影響檔案共用的層級。

#### <a name="the-networking-blade"></a>網路分頁
[網路] 區段可讓您設定網路功能選項。 這些設定是用來建立儲存體帳戶的選擇性設定，並可在稍後視需要進行設定。 如需這些選項的詳細資訊，請參閱 [Azure 檔案儲存體網路功能考慮](storage-files-networking-overview.md)。

#### <a name="the-advanced-blade"></a>Advanced blade
[Advanced] 區段包含 Azure 檔案共用的數個重要設定：

- **需要安全傳輸**：此欄位會指出儲存體帳戶是否需要在傳輸中加密，以便與儲存體帳戶進行通訊。 建議您將這項功能保持啟用狀態，不過，如果您需要 SMB 2.1 支援，您必須停用此功能。 建議您停用加密，以將您的儲存體帳戶存取許可權制為具有服務端點和/或私人端點的虛擬網路。
- **大型檔案共用**：此欄位可讓檔案共用的儲存體帳戶跨越最多 100 TiB。 啟用這項功能會將您的儲存體帳戶限制為僅限本機冗余和區域多餘的儲存體選項。 針對大型檔案共用啟用 GPv2 儲存體帳戶之後，您就無法停用大型檔案共用功能。 FileStorage 儲存體帳戶 (premium 檔案共用的儲存體帳戶) 沒有此選項，因為所有 premium 檔案共用最多可擴大至 100 TiB。 

![適用于 Azure 檔案儲存體的重要 advanced 設定的螢幕擷取畫面](media/storage-how-to-create-file-share/create-storage-account-3.png)

[高階] 索引標籤中可用的其他設定 (blob 虛刪除、Azure Data Lake 儲存體 gen 2 的階層式命名空間，以及 blob 儲存體) 的 NFSv3 不適用於 Azure 檔案儲存體。

#### <a name="tags"></a>標籤
標籤為成對的名稱和值，可讓您將相同的標籤套用至多個資源與資源群組，以便為資源分類及檢視合併的帳單。 這些是選擇性的，可在建立儲存體帳戶之後套用。

#### <a name="review--create"></a>檢閱 + 建立
建立儲存體帳戶的最後一個步驟是選取 [**審核 + 建立**] 索引標籤上的 [**建立**] 按鈕。如果未填入儲存體帳戶的所有必要欄位，則無法使用此按鈕。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
若要使用 PowerShell 建立儲存體帳戶，我們會使用 `New-AzStorageAccount` Cmdlet。 這個 Cmdlet 有許多選項;只會顯示所需的選項。 若要深入瞭解 advanced 選項，請參閱[ `New-AzStorageAccount` Cmdlet 檔](/powershell/module/az.storage/new-azstorageaccount)。

為了簡化儲存體帳戶和後續檔案共用的建立，我們會將數個參數儲存在變數中。 您可以將變數內容取代為您想要的任何值，但請注意，儲存體帳戶名稱必須是全域唯一的。

```powershell
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

若要建立能夠儲存標準 Azure 檔案共用的儲存體帳戶，我們將使用下列命令。 此 `-SkuName` 參數與所需的冗余類型相關，如果您想要異地冗余或地理區域冗余的儲存體帳戶，您也必須移除 `-EnableLargeFileShare` 參數。

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

若要建立能夠儲存 premium Azure 檔案共用的儲存體帳戶，我們將使用下列命令。 請注意， `-SkuName` 參數已變更為包含 `Premium` 本地 () 所需的本機冗余層級 `LRS` 。 `-Kind`參數是， `FileStorage` 而不是 `StorageV2` 因為必須在 FileStorage 儲存體帳戶中建立 premium 檔案共用，而不是 GPv2 儲存體帳戶。

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
若要使用 Azure CLI 建立儲存體帳戶，我們將使用 az storage account create 命令。 此命令有許多選項;只會顯示所需的選項。 若要深入瞭解 advanced 選項，請參閱[ `az storage account create` 命令檔](/cli/azure/storage/account)集。

為了簡化儲存體帳戶和後續檔案共用的建立，我們會將數個參數儲存在變數中。 您可以將變數內容取代為您想要的任何值，但請注意，儲存體帳戶名稱必須是全域唯一的。

```bash
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

若要建立能夠儲存標準 Azure 檔案共用的儲存體帳戶，我們將使用下列命令。 此 `--sku` 參數與所需的冗余類型相關，如果您想要異地冗余或地理區域冗余的儲存體帳戶，您也必須移除 `--enable-large-file-share` 參數。

```bash
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

若要建立能夠儲存 premium Azure 檔案共用的儲存體帳戶，我們將使用下列命令。 請注意， `--sku` 參數已變更為包含 `Premium` 本地 () 所需的本機冗余層級 `LRS` 。 `--kind`參數是， `FileStorage` 而不是 `StorageV2` 因為必須在 FileStorage 儲存體帳戶中建立 premium 檔案共用，而不是 GPv2 儲存體帳戶。

```bash
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>建立檔案共用
一旦您建立儲存體帳戶之後，剩下的就是建立您的檔案共用。 無論您使用的是 premium 檔案共用或標準檔案共用，此程式的流程大多相同。 您應該考慮下列差異。

標準檔案共用可能會部署到其中一個標準層：交易優化 (預設) 、經常性存取或非經常性存取。 這是每個檔案共用層，不受儲存體帳戶的 **blob 存取層** 影響 (此屬性只會與 Azure blob 儲存體相關-它與所有) 的 Azure 檔案儲存體無關。 部署共用之後，您可以隨時變更共用的層級。 Premium 檔案共用無法直接轉換成任何標準層中的標準檔案共用。

> [!Important]  
> 您可以在 GPv2 儲存體帳戶類型中的各層之間移動檔案共用 (交易最佳化、經常性存取和非經常性存取)。 各層之間的共用移動會產生交易：從經常性存取層移至非經常性存取層，會對共用中的每個檔案產生非經常性存取層的寫入交易費用，而從非經常性存取層移至經常性存取層，則會對共用的每個檔案產生非經常性存取層的讀取交易費用。

[ **配額** ] 屬性工作表示 premium 和標準檔案共用之間稍微有不同的內容：

- 針對標準檔案共用，它是 Azure 檔案共用的上限，但使用者無法前往。 標準檔案共用配額的主要用途是預算：「我不希望這個檔案共用成長超過這個時間點」。 如果未指定配額，標準檔案共用最多可以跨越 100 TiB (或 5 TiB 如果未針對儲存體帳戶) 設定大型檔案共用屬性。

- 針對 premium 檔案共用，配額會超載為平均布 **建大小**。 布建的大小是您將計費的金額，不論實際使用量為何。 當您布建 premium 檔案共用時，您會想要考慮兩個因素： 1) 共用的未來成長從空間使用量的觀點來看，2) 工作負載所需的 IOPS。 每個布建的 GiB 可享有額外的保留和高載 IOPS。 如需有關如何規劃 premium 檔案共用的詳細資訊，請參閱布建 [premium](storage-files-planning.md#understanding-provisioning-for-premium-file-shares)檔案共用。

# <a name="portal"></a>[入口網站](#tab/azure-portal)
如果您剛才建立儲存體帳戶，您可以選取 [ **移至資源**]，從部署畫面流覽至該帳戶。 如果您先前已建立儲存體帳戶，您可以透過包含該帳戶的資源群組來流覽至該帳戶。 一旦在儲存體帳戶中，選取標示為 [檔案 **共用** ] 的磚 (您也可以透過儲存體帳戶) 的瀏覽目錄至檔案 **共用** 。

![[檔案共用] 磚的螢幕擷取畫面](media/storage-how-to-create-file-share/create-file-share-1.png)

在檔案共用清單中，您應該會看到先前在此儲存體帳戶中建立的任何檔案共用;如果尚未建立任何檔案共用，則為空的資料表。 選取 [ **+ 檔案共用** ] 以建立新的檔案共用。

新的檔案共用分頁應該會出現在螢幕上。 完成 [新增檔案共用] 分頁中的欄位，以建立檔案共用：

- **名稱**：要建立的檔案共用名稱。
- **配額**：標準檔案共用的檔案共用配額;premium 檔案共用之檔案共用的布建大小。
- **層**：為檔案共用選取的層。 此欄位僅適用于 **一般用途 (GPv2) 儲存體帳戶**。 您可以選擇「交易優化」、「經常性存取」或「非經常性存取」。 共用的層級可以隨時變更。 建議您在遷移期間挑選最高的層級，以將交易費用降到最低，並在遷移完成後切換至較低的層級。

選取 [ **建立** ] 以完成建立新的共用。 請注意，如果您的儲存體帳戶位於虛擬網路中，除非您的用戶端也位於虛擬網路中，否則您將無法成功建立 Azure 檔案共用。 您也可以使用 Azure PowerShell Cmdlet 來解決這個時間點的限制 `New-AzRmStorageShare` 。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
您可以使用 Cmdlet 來建立 Azure 檔案共用 [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) 。 下列 PowerShell 命令假設您已設定變數 `$resourceGroupName` ，並 `$storageAccountName` 在使用 Azure PowerShell 區段建立儲存體帳戶的上方定義。 

下列範例示範如何使用參數建立具有明確層的檔案共用 `-AccessTier` 。 這需要使用範例中所示的預覽 Az. Storage 模組。 如果未指定階層，可能是因為您使用的是 GA Az. Storage 模組，或者因為您未包含此命令，則標準檔案共用的預設層會進行交易優化。

> [!Important]  
> 若是 premium 檔案共用， `-QuotaGiB` 參數會參考檔案共用的布建大小。 檔案共用的布建大小是您將收取的費用，而不論使用量為何。 標準檔案共用的計費依據是使用方式，而不是布建的大小。

```powershell
# Update the Azure storage module to use the preview version. You may need to close and 
# reopen PowerShell before running this command. If you are running PowerShell 5.1, ensure 
# the following:
# - Run the below cmdlets as an administrator.
# - Have PowerShellGet 2.2.3 or later. Uncomment the following line to check.
# Get-Module -ListAvailable -Name PowerShellGet
Remove-Module -Name Az.Storage -ErrorAction SilentlyContinue
Uninstall-Module -Name Az.Storage
Install-Module -Name Az.Storage -RequiredVersion "2.1.1-preview" -AllowClobber -AllowPrerelease 

# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2 
# storage accounts. Standard tiers are only available in standard storage accounts. 
$shareName = "myshare"

New-AzRmStorageShare `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $storageAccountName `
        -Name $shareName `
        -AccessTier TransactionOptimized `
        -QuotaGiB 1024 | `
    Out-Null
```

> [!Note]  
> 預覽 Az. 儲存體 PowerShell 模組中提供透過 PowerShell 設定和變更階層的功能。 這些 Cmdlet 或其輸出可能會在發行至正式推出的 Az. 儲存體 PowerShell 模組之前變更，因此請記住這點。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
您可以使用命令來建立 Azure 檔案共用 [`az storage share-rm create`](https://docs.microsoft.com/cli/azure/storage/share-rm?view=azure-cli-latest&preserve-view=true#az_storage_share_rm_create) 。 下列 Azure CLI 命令假設您已設定變數 `$resourceGroupName` ，並 `$storageAccountName` 在使用 Azure CLI 區段建立儲存體帳戶的上方定義。

您可以在最新的 Azure CLI 更新中，取得用來建立檔案共用或將檔案共用移至特定層的功能。 更新 Azure CLI 是您所使用的作業系統/Linux 散發套件專用的。 如需有關如何更新系統上 Azure CLI 的指示，請參閱 [安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)。

> [!Important]  
> 若是 premium 檔案共用， `--quota` 參數會參考檔案共用的布建大小。 檔案共用的布建大小是您將收取的費用，而不論使用量為何。 標準檔案共用的計費依據是使用方式，而不是布建的大小。

```bash
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "TransactionOptimized" \
    --quota 1024 \
    --output none
```

> [!Note]  
> 以參數設定階層的功能 `--access-tier` 會在最新的 Azure CLI 套件中提供預覽。 此命令或其輸出可能會在標示為已正式推出之前變更，因此請記住這點來建立腳本。

---

> [!Note]  
> 您的檔案共用名稱必須是全部小寫。 如需命名檔案共用和檔案的完整詳細資訊，請參閱 [命名和參考共用、目錄、檔案和中繼資料](https://msdn.microsoft.com/library/azure/dn167011.aspx)。

### <a name="changing-the-tier-of-an-azure-file-share"></a>變更 Azure 檔案共用的層級
在一般用途 v2 中部署的檔案共用 ** (GPv2) 儲存體帳戶** 可以位於交易優化、經常性存取或非經常性存取層。 您可以隨時變更 Azure 檔案共用的層級，其受限於以上所述的交易成本。

# <a name="portal"></a>[入口網站](#tab/azure-portal)
在 [主要儲存體帳戶] 頁面上，選取 [ **檔案共用]，選取標示**  為 [檔案 **共用** ] 的磚 (您也可以透過儲存體帳戶) 的瀏覽目錄至檔案 **共用** 。

![[檔案共用] 磚的螢幕擷取畫面](media/storage-how-to-create-file-share/create-file-share-1.png)

在檔案共用的表格清單中，選取您要變更層的檔案共用。 在 [檔案共用總覽] 頁面上，從功能表中選取 [ **變更** 階層]。

![[檔案共用] 總覽頁面的螢幕擷取畫面，其中已醒目提示變更層按鈕](media/storage-how-to-create-file-share/change-tier-0.png)

在產生的對話方塊中，選取所需的層級：交易優化、經常性或非經常性存取。

![[變更層] 對話方塊的螢幕擷取畫面](media/storage-how-to-create-file-share/change-tier-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
下列 PowerShell Cmdlet 會假設您已設定 `$resourceGroupName` 、 `$storageAccountName` 、變數， `$shareName` 如本檔稍早的章節所述。

```PowerShell
# This cmdlet requires Az.Storage version 2.1.1-preview, which is installed
# in the earlier example.
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
下列 Azure CLI 命令假設您已設定 `$resourceGroupName` 、 `$storageAccountName` 和 `$shareName` 變數，如本檔稍早的章節所述。

```bash
az storage share-rm update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Cool"
```

---

## <a name="next-steps"></a>後續步驟
- [規劃 Azure 檔案儲存體部署](storage-files-planning.md) 或 [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)。 
- [網路功能總覽](storage-files-networking-overview.md)。
- 在 [Windows](storage-how-to-use-files-windows.md)、 [macOS](storage-how-to-use-files-mac.md)和 [Linux](storage-how-to-use-files-linux.md)上連接並掛接檔案共用。