---
title: 建立 Azure 檔案共用
titleSuffix: Azure Files
description: 如何使用 Azure 入口網站、PowerShell 或 Azure CLI 建立 Azure 檔案共用。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ed6abbac7c5953eaec4fa4584248d0d98b49ba63
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596866"
---
# <a name="create-an-azure-file-share"></a>建立 Azure 檔案共用
若要建立 Azure 檔案共用，您需要回答下列三個關於如何使用的問題：

- **您的 Azure 檔案共用有哪些效能需求？**  
    Azure 檔案儲存體提供標準檔案共用，其裝載于以硬碟為基礎（HDD 型）的硬體上，以及高階檔案共用，其裝載于以固態磁片為基礎（SSD 型）的硬體上。

- **您需要何種大小的檔案共用？**  
    標準檔案共用最多可達 100 TiB，但預設不會啟用此功能;如果您需要大於 5 TiB 的檔案共用，您必須為儲存體帳戶啟用「大型檔案共用」功能。 Premium 檔案共用最多可以跨越 100 TiB，而不需要任何特殊設定，不過會布建 premium 檔案共用，而不是隨用隨付，如同標準檔案共用。 這表示布建檔案共用的速度遠超過您所需的空間，將會增加儲存體的總成本。

- **您的 Azure 檔案共用有哪些多餘的需求？**  
    標準檔案共用提供本機的冗余（LRS）、區域冗余（ZRS）、異地冗余（GRS）或異地區域冗余（切換）儲存體，不過，只有在本機的多餘和區域的多餘檔案共用才支援大型檔案共用功能。 Premium 檔案共用不支援任何形式的異地冗余。

    在大部分區域中，提供儲存體帳戶和區域冗余的高階檔案共用可在較小的區域子集中使用。 若要找出您的區域目前是否有 premium 檔案共用，請參閱 Azure 的[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=storage)頁面。 如需有關支援 ZRS 之區域的詳細資訊，請參閱[Azure 儲存體冗余](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

如需這三個選項的詳細資訊，請參閱[規劃 Azure 檔案儲存體部署](storage-files-planning.md)。

## <a name="prerequisites"></a>Prerequisites
- 本文假設您已建立 Azure 訂用帳戶。 如果您還沒有訂用帳戶，則先建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)，再開始操作。
- 如果您想要使用 Azure PowerShell，請[安裝最新版本](https://docs.microsoft.com/powershell/azure/install-az-ps)。
- 如果您想要使用 Azure CLI，請[安裝最新版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="create-a-storage-account"></a>建立儲存體帳戶
Azure 檔案共用會部署到*儲存體帳戶*，這是代表共用儲存體集區的最上層物件。 此儲存體集區可以用來部署多個檔案共用。 

Azure 針對客戶可能擁有的不同儲存體案例支援多種類型的儲存體帳戶，但 Azure 檔案儲存體的儲存體帳戶有兩種主要類型。 您需要建立哪個儲存體帳戶類型，取決於您要建立標準檔案共用還是 premium 檔案共用： 

- **一般目的版本2（GPv2）儲存體帳戶**： GPv2 儲存體帳戶可讓您在以標準/硬碟為基礎（HDD）的硬體上部署 Azure 檔案共用。 除了儲存 Azure 檔案共用，GPv2 儲存體帳戶還可以儲存其他儲存體資源，例如 blob 容器、佇列或資料表。 

- **FileStorage 儲存體帳戶**： FileStorage 儲存體帳戶可讓您在 premium/固態磁片型（SSD 型）硬體上部署 Azure 檔案共用。 FileStorage 帳戶只能用來儲存 Azure 檔案共用;不能在 FileStorage 帳戶中部署其他儲存體資源（blob 容器、佇列、資料表等等）。

# <a name="portal"></a>[入口網站](#tab/azure-portal)
若要透過 Azure 入口網站建立儲存體帳戶，請從儀表板選取 [ **+ 建立資源**]。 在產生的 Azure Marketplace 搜尋 視窗中，搜尋 **儲存體帳戶**，然後選取產生的搜尋結果。 這會導致儲存體帳戶的總覽頁面;選取 [**建立**] 以繼續進行儲存體帳戶建立嚮導。

![瀏覽器中 [儲存體帳戶] 快速建立選項的螢幕擷取畫面](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>基本區段
建立儲存體帳戶的第一個完成區段標示為 [**基本**]。 這包含建立儲存體帳戶的所有必要欄位。 若要建立 GPv2 儲存體帳戶，請確定 [**效能**] 選項按鈕已設定為 [*標準*]，而且已選取 [**帳戶類型**] 下拉式清單至 [ *StorageV2] （一般用途 v2）* 。

![[效能] 選項按鈕的螢幕擷取畫面，其中已選取 [標準] 和 [已選取 StorageV2 的帳戶類型]](media/storage-how-to-create-file-share/create-storage-account-1.png)

若要建立 FileStorage 儲存體帳戶，請確定 [**效能**] 選項按鈕已設定為 [ *Premium* ]，而且已選取 [**帳戶類型**] 下拉式清單來*FileStorage*。

![[效能] 選項按鈕的螢幕擷取畫面，其中已選取 [Premium] 和 [已選取 FileStorage 的帳戶種類]](media/storage-how-to-create-file-share/create-storage-account-2.png)

其他基本欄位與儲存體帳戶的選擇無關：
- **訂**用帳戶：要部署到其中的儲存體帳戶的訂用帳戶。 
- **資源群組**：要部署到其中的儲存體帳戶的資源群組。 您可以建立新的資源群組，或使用現有的資源群組。 資源群組是用來群組 Azure 服務的邏輯容器。 當您建立儲存體帳戶時，可以選擇建立新的資源群組，或使用現有的資源群組。
- **儲存體帳戶名稱**：要建立的儲存體帳戶資源的名稱。 此名稱必須是全域唯一的，否則可以是您想要的任何名稱。 當您透過 SMB 掛接 Azure 檔案共用時，將會使用儲存體帳戶名稱做為伺服器名稱。
- **位置**：要在其中部署儲存體帳戶的區域。 這可以是與資源群組或任何其他可用區域相關聯的區域。
- 複寫：雖然這會標示為複寫，但此欄位實際上表示有**重複** **的複本;** 這是所需的冗余層級： [本機冗余] （LRS）、[區域冗余] （ZRS）、[異地冗余] （GRS）和 [異地區域冗余]。 此下拉式清單也包含讀取權限異地冗余（RA-GRS）和讀取權限異地區域冗余（RA-切換），其不適用於 Azure 檔案共用;在儲存體帳戶中，使用這些選取的檔案共用，會分別是異地多餘或異地區域冗余。 視您的區域或選取的儲存體帳戶類型而定，可能不允許某些多餘的選項。
- **存取層**：此欄位不適用於 Azure 檔案儲存體，因此您可以選擇其中一個選項按鈕。

#### <a name="the-networking-blade"></a>網路功能分頁
[網路] 區段可讓您設定網路功能選項。 這些設定在建立儲存體帳戶時是選擇性的，而且可以在稍後視需要進行設定。 如需這些選項的詳細資訊，請參閱[Azure 檔案儲存體的網路功能考慮](storage-files-networking-overview.md)。

#### <a name="the-advanced-blade"></a>Advanced 分頁
Advanced 區段包含 Azure 檔案共用的幾項重要設定：

- **需要安全傳輸**：此欄位指出儲存體帳戶在傳輸時是否需要加密以進行儲存體帳戶的通訊。 我們建議您保留此功能，但如果您需要 SMB 2.1 支援，則必須停用此功能。 我們建議您停用加密，將您的儲存體帳戶存取許可權制為具有服務端點和/或私人端點的虛擬網路。
- **大型檔案共用**：此欄位會啟用最多 100 TiB 的檔案共用儲存體帳戶。 啟用這項功能會將您的儲存體帳戶限制為僅在本機多餘和區域多餘的儲存體選項。 當 GPv2 儲存體帳戶已啟用大型檔案共用之後，您就無法停用大型檔案共用功能。 FileStorage 儲存體帳戶（premium 檔案共用的儲存體帳戶）沒有此選項，因為所有 premium 檔案共用都可以相應增加至 100 TiB。 

![適用于的重要高級設定的螢幕擷取畫面 Azure 檔案儲存體](media/storage-how-to-create-file-share/create-storage-account-3.png)

[高級] 索引標籤中可用的其他設定（blob 虛刪除、Azure Data Lake 儲存體 gen 2 的階層式命名空間，以及 blob 儲存體的 NFSv3）不適用於 Azure 檔案儲存體。

#### <a name="tags"></a>Tags
標籤是成對的名稱/值，可讓您將相同的標籤套用至多個資源和資源群組，藉此將資源分類及查看合併計費。 這些是選擇性的，而且可以在建立儲存體帳戶之後套用。

#### <a name="review--create"></a>檢閱 + 建立
建立儲存體帳戶的最後一個步驟是選取 [**審核 + 建立**] 索引標籤上的 [**建立**] 按鈕。如果未填入儲存體帳戶的所有必要欄位，此按鈕將無法使用。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
若要使用 PowerShell 建立儲存體帳戶，我們將使用 `New-AzStorageAccount` Cmdlet。 此 Cmdlet 有許多選項;只會顯示必要的選項。 若要深入瞭解 advanced 選項，請參閱[`New-AzStorageAccount` Cmdlet 檔](/powershell/module/az.storage/new-azstorageaccount)。

為了簡化儲存體帳戶和後續檔案共用的建立，我們會將數個參數儲存在變數中。 您可以使用您想要的任何值來取代變數內容，但請注意，儲存體帳戶名稱必須是全域唯一的。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

若要建立能夠儲存標準 Azure 檔案共用的儲存體帳戶，我們將使用下列命令。 `-SkuName` 參數與所需的冗余類型相關聯;如果您想要異地多餘或異地區域的儲存體帳戶，您也必須移除 `-EnableLargeFileShare` 參數。

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

若要建立能夠儲存高階 Azure 檔案共用的儲存體帳戶，我們將使用下列命令。 請注意，`-SkuName` 參數已變更為同時包含 `Premium` 和所需的本機冗余層級（`LRS`）。 `-Kind` 參數是 `FileStorage` 而不是 `StorageV2`，因為 premium 檔案共用必須建立在 FileStorage 儲存體帳戶中，而不是 GPv2 儲存體帳戶中。

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
若要使用 Azure CLI 建立儲存體帳戶，我們將使用 az storage account create 命令。 此命令有許多選項;只會顯示必要的選項。 若要深入瞭解 advanced 選項，請參閱[`az storage account create` 命令檔](/cli/azure/storage/account)。

為了簡化儲存體帳戶和後續檔案共用的建立，我們會將數個參數儲存在變數中。 您可以使用您想要的任何值來取代變數內容，但請注意，儲存體帳戶名稱必須是全域唯一的。

```azurecli-interactive
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

若要建立能夠儲存標準 Azure 檔案共用的儲存體帳戶，我們將使用下列命令。 `--sku` 參數與所需的冗余類型相關聯;如果您想要異地多餘或異地區域的儲存體帳戶，您也必須移除 `--enable-large-file-share` 參數。

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

若要建立能夠儲存高階 Azure 檔案共用的儲存體帳戶，我們將使用下列命令。 請注意，`--sku` 參數已變更為同時包含 `Premium` 和所需的本機冗余層級（`LRS`）。 `--kind` 參數是 `FileStorage` 而不是 `StorageV2`，因為 premium 檔案共用必須建立在 FileStorage 儲存體帳戶中，而不是 GPv2 儲存體帳戶中。

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>建立檔案共用
建立儲存體帳戶之後，剩下的就是建立您的檔案共用。 無論您是使用 premium 檔案共用還是標準檔案共用，此程式基本上都相同。 主要差異在於**配額**和其代表的內容。

針對標準檔案共用，它是 Azure 檔案共用的上限，使用者之後就無法繼續進行。 標準檔案共用配額的主要用途是預算：「我不希望此檔案共用成長超過此時間點」。 如果沒有指定配額，標準檔案共用最多可以跨越 100 TiB （如果未針對儲存體帳戶設定 [大型檔案共用] 屬性，則為5個 TiB）。

針對 premium 檔案共用，配額會多載為平均布**建大小**。 [已布建大小] 是您要支付的費用，不論實際使用量為何。 當您布建 premium 檔案共用時，您會想要考慮兩個因素：1）從空間使用量觀點來看，未來的共用成長，以及2）您的工作負載所需的 IOPS。 每個布建的 GiB 都可享有額外的保留和高載 IOPS。 如需如何規劃 premium 檔案共用的詳細資訊，請參閱布建[premium](storage-files-planning.md#understanding-provisioning-for-premium-file-shares)檔案共用。

# <a name="portal"></a>[入口網站](#tab/azure-portal)
如果您剛建立儲存體帳戶，可以選取 [**移至資源**]，從 [部署] 畫面流覽至它。 如果您先前已建立儲存體帳戶，您可以透過包含它的資源群組來流覽。 在儲存體帳戶中，選取標示為 [檔案**共用**] 的磚（您也可以透過儲存體帳戶的目錄來流覽至檔案**共用**）。

![[檔案共用] 磚的螢幕擷取畫面](media/storage-how-to-create-file-share/create-file-share-1.png)

在檔案共用清單中，您應該會看到先前在此儲存體帳戶中建立的任何檔案共用;如果尚未建立任何檔案共用，則為空的資料表。 選取 [ **+ 檔案共用**] 以建立新的檔案共用。

[新增檔案共用] 分頁應該會出現在畫面上。 完成 [新增檔案共用] 分頁中的欄位，以建立檔案共用：

- **名稱**：要建立的檔案共用名稱。
- **配額**：標準檔案共用的檔案共用配額;premium 檔案共用的檔案共用的布建大小。

選取 [**建立**] 以完成建立新的共用。 請注意，如果您的儲存體帳戶位於虛擬網路中，除非您的用戶端也在虛擬網路中，否則您將無法成功建立 Azure 檔案共用。 您也可以使用 Azure PowerShell `New-AzRmStorageShare` Cmdlet 來解決這個時間點的限制。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
您可以使用[`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) Cmdlet 來建立 Azure 檔案共用。 下列 PowerShell 命令假設您已在使用 Azure PowerShell 建立儲存體帳戶一節中，將變數設定 `$resourceGroupName`，並 `$storageAccountName` 如上所定義。 

> [!Important]  
> 針對 premium 檔案共用，`-QuotaGiB` 參數會參考檔案共用的布建大小。 檔案共用的布建大小就是您要支付的金額，而不論使用方式為何。 標準檔案共用是根據使用量來計費，而不是布建大小。

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 1024 | Out-Null
```

> [!Note]  
> 您的檔案共用名稱必須是全部小寫。 如需有關為檔案共用和檔案命名的完整詳細資料，請參閱 [命名和參考共用、目錄、檔案和中繼資料](https://msdn.microsoft.com/library/azure/dn167011.aspx)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
在我們可以使用 Azure CLI 建立 Azure 檔案共用之前，您必須取得儲存體帳戶金鑰，以授權檔案共用建立作業。 這可以透過[`az storage account keys list`](/cli/azure/storage/account/keys)命令來完成：

```azurecli-interactive
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

擁有儲存體帳戶金鑰之後，您可以使用[`az storage share create`](/cli/azure/storage/share)命令來建立 Azure 檔案共用。 

> [!Important]  
> 針對 premium 檔案共用，`--quota` 參數會參考檔案共用的布建大小。 檔案共用的布建大小就是您要支付的金額，而不論使用方式為何。 標準檔案共用是根據使用量來計費，而不是布建大小。

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

如果儲存體帳戶包含在虛擬網路中，而且您叫用此命令的電腦不是虛擬網路的一部分，此命令將會失敗。 如上面所述，您可以使用 Azure PowerShell `New-AzRmStorageShare` Cmdlet 來解決這個時間點限制，或從屬於虛擬網路一部分的電腦執行 Azure CLI，包括透過 VPN 連線。

---

> [!Note]  
> 您的檔案共用名稱必須是全部小寫。 如需有關為檔案共用和檔案命名的完整詳細資料，請參閱 [命名和參考共用、目錄、檔案和中繼資料](https://msdn.microsoft.com/library/azure/dn167011.aspx)。

## <a name="next-steps"></a>後續步驟
- [規劃 Azure 檔案儲存體的部署](storage-files-planning.md)，或[規劃 Azure 檔案同步部署](storage-sync-files-planning.md)。 
- [網路功能總覽](storage-files-networking-overview.md)。
- 在[Windows](storage-how-to-use-files-windows.md)、 [macOS](storage-how-to-use-files-mac.md)和[Linux](storage-how-to-use-files-linux.md)上連接並掛接檔案共用。