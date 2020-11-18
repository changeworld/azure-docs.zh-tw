---
title: 變更儲存體帳戶的複寫方式 \(部分機器翻譯\)
titleSuffix: Azure Storage
description: 瞭解如何變更現有儲存體帳戶中的資料複寫方式。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/24/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 228595bf633ef0545a13abe19308e49da82cf75a
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844007"
---
# <a name="change-how-a-storage-account-is-replicated"></a>變更儲存體帳戶的複寫方式 \(部分機器翻譯\)

Azure 儲存體會一律儲存資料的多個複本，以保護該資料不受計劃性和非計劃性事件影響，包括暫時性硬體故障、網路或電力中斷和大規模天然災害。 備援可確保儲存體帳戶符合 [Azure 儲存體的服務等級協定 (SLA)](https://azure.microsoft.com/support/legal/sla/storage/)，即使遇到失敗時也一樣。

Azure 儲存體提供下列類型的複寫：

- 本地備援儲存體 (LRS)
- 區域備援儲存體 (ZRS)
- 異地冗余儲存體 (GRS) 或讀取權限異地冗余儲存體 (RA-GRS) 
- 地理區域-冗余儲存體 (GZRS) 或讀取權限地理區域-冗余儲存體 (RA-GZRS) 

如需每個選項的總覽，請參閱 [Azure 儲存體冗余](storage-redundancy.md)。

## <a name="switch-between-types-of-replication"></a>切換複寫類型

您可以將儲存體帳戶從某種類型的複寫切換至任何其他類型，但某些案例比其他類型更直接。 如果您想要新增或移除次要區域的異地複寫或讀取權限，您可以使用 Azure 入口網站、PowerShell 或 Azure CLI 來更新複寫設定。 但是，如果您想要變更在主要區域中複寫資料的方式，方法是從 LRS 移至 ZRS 或反之亦然，然後您必須執行手動遷移。

下表概述如何從每種複寫類型切換到另一種類型：

| 開關 | ...LRS | ...GRS/RA-GRS | ...ZRS | ...GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>...從 LRS</b> | 不適用 | 使用 Azure 入口網站、PowerShell 或 CLI 來變更複寫設定<sup>1</sup> | 執行手動遷移 <br /><br /> 或者 <br /><br /> 要求即時移轉 | 執行手動遷移 <br /><br /> 或者 <br /><br /> 先切換至 GRS/RA-GRS，然後要求即時移轉<sup>1</sup> |
| <b>...從 GRS/RA-GRS</b> | 使用 Azure 入口網站、PowerShell 或 CLI 來變更複寫設定 | 不適用 | 執行手動遷移 <br /><br /> 或者 <br /><br /> 先切換至 LRS，然後再要求即時移轉 | 執行手動遷移 <br /><br /> 或者 <br /><br /> 要求即時移轉 |
| <b>...從 ZRS</b> | 執行手動遷移 | 執行手動遷移 | 不適用 | 使用 Azure 入口網站、PowerShell 或 CLI 來變更複寫設定<sup>1、2</sup> |
| <b>...從 GZRS/RA-GZRS</b> | 執行手動遷移 | 執行手動遷移 | 使用 Azure 入口網站、PowerShell 或 CLI 來變更複寫設定 | 不適用 |

<sup>1</sup> 會產生一次性出口費用。<br />
下欄區域不支援從<sup>ZRS 轉換為</sup>GZRS/RA-GZRS，反之亦然：美國東部2、美國東部、西歐。

> [!CAUTION]
> 如果您已針對 (RA-) GRS 或 (RA-) GZRS 帳戶執行 [帳戶容錯移轉](storage-disaster-recovery-guidance.md) ，則在容錯移轉之後，該帳戶會在新的主要區域中以本機方式重複。 不支援針對容錯移轉所產生的 LRS 帳戶即時移轉至 ZRS 或 GZRS。 即使在所謂的容錯回復作業情況下，也是如此。 例如，如果您執行從 GZRS 到次要區域中 LRS 的帳戶容錯移轉，然後再次將它設定為使用 RA GRS，並執行另一個帳戶容錯移轉至原始主要區域，您就無法將原始即時移轉的支援與主要區域中的遠端協助 GZRS 聯繫。 相反地，您必須手動遷移至 ZRS 或 GZRS。

## <a name="change-the-replication-setting"></a>變更複寫設定

您可以使用 Azure 入口網站、PowerShell 或 Azure CLI 來變更儲存體帳戶的複寫設定，只要您未變更在主要區域中複寫資料的方式即可。 如果您要從主要區域中的 LRS 遷移至主要區域中的 ZRS，或反之亦然，則必須執行手動遷移或即時移轉。

變更儲存體帳戶的複寫方式，不會導致應用程式的停機時間。

# <a name="portal"></a>[入口網站](#tab/portal)

若要在 Azure 入口網站中變更儲存體帳戶的 [冗余] 選項，請遵循下列步驟：

1. 在 Azure 入口網站中巡覽至您的儲存體帳戶。
1. 選取 [ **設定** ]。
1. 更新複寫 **設定。**

![顯示如何在入口網站中變更複寫選項的螢幕擷取畫面](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 變更儲存體帳戶的冗余選項，請呼叫 [new-azstorageaccount](/powershell/module/az.storage/set-azstorageaccount) 命令並指定 `-SkuName` 參數：

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 變更儲存體帳戶的冗余選項，請呼叫 [az storage account update](/cli/azure/storage/account#az-storage-account-update) 命令，並指定 `--sku` 參數：

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs-gzrs-or-ra-gzrs"></a>執行手動遷移至 ZRS、GZRS 或 RA-GZRS

如果您想要變更在主要區域中複寫儲存體帳戶中資料的方式，方法是從 LRS 移至 ZRS 或反之亦然，然後您可以選擇執行手動遷移。 手動移轉比即時移轉更具彈性。 您可以控制手動遷移的時間，如果您需要在特定日期內完成遷移，請使用此選項。

當您從 LRS 手動遷移至主要區域中的 ZRS，或反之亦然時，目的地儲存體帳戶可以是異地冗余，也可以設定為對次要區域的讀取權限。 例如，您可以在一個步驟中將 LRS 帳戶遷移至 GZRS 或 RA GZRS 帳戶。

手動移轉可能造成應用程式停機。 如果您的應用程式要求高可用性，Microsoft 也提供即時移轉選項。 即時移轉是一種不需停機的就地移轉。

透過手動遷移，您可以從現有的儲存體帳戶將資料複製到在主要區域中使用 ZRS 的新儲存體帳戶。 若要執行手動遷移，您可以使用下列其中一個選項：

- 使用現有的工具（例如 AzCopy）、其中一個 Azure 儲存體用戶端程式庫或可靠的協力廠商工具來複製資料。
- 如果您熟悉 Hadoop 或 HDInsight，可以將來源儲存體帳戶和目的地儲存體帳戶帳戶連結至您的叢集。 然後，使用 DistCp 之類的工具來平行處理資料複製程序。

## <a name="request-a-live-migration-to-zrs-gzrs-or-ra-gzrs"></a>要求即時移轉至 ZRS、GZRS 或 RA-GZRS

如果您需要將儲存體帳戶從 LRS 遷移至主要區域中的 ZRS，而不需要應用程式停機時間，您可以向 Microsoft 要求即時移轉。 若要從 LRS 遷移至 GZRS 或 RA-GZRS，請先切換至 GRS 或 RA-GRS，然後要求即時移轉。 同樣地，您可以要求從 GRS 或 GRS 到 GZRS 或 RA-GZRS 的即時移轉。 若要從 GRS 或 GRS 遷移至 ZRS，請先切換至 LRS，然後要求即時移轉。

在即時移轉期間，您可以存取儲存體帳戶中的資料，而不會遺失耐久性或可用性。 在遷移過程中會保留 Azure 儲存體 SLA。 沒有與即時移轉相關聯的資料遺失。 在遷移之後，服務端點、存取金鑰、共用存取簽章和其他帳戶選項都會保持不變。

ZRS 僅支援一般用途 v2 帳戶，因此請務必先升級您的儲存體帳戶，再將即時移轉的要求提交至 ZRS。 如需詳細資訊，請參閱[升級至一般用途 v2 儲存體帳戶](storage-account-upgrade.md)。 儲存體帳戶必須包含要透過即時移轉來遷移的資料。

只有針對使用 LRS 或 GRS 複寫的儲存體帳戶，才支援即時移轉。 如果您的帳戶使用 RA-GRS，則必須先將帳戶複寫類型變更為 LRS 或 GRS，再繼續進行操作。 這個中繼步驟會在移轉之前，先將 RA-GRS 所提供的次要唯讀端點移除。

雖然 Microsoft 會迅速處理您的即時移轉要求，但不保證移轉何時會完成。 如果您需要在特定日期之前將資料移轉至 ZRS，則 Microsoft 建議您改為執行手動移轉。 一般而言，您帳戶中的資料越多，移轉該資料所需的時間就越長。

如果有下列情況，您必須執行手動遷移：

- 您想要將資料移轉到與來源帳戶不同的區域中的 ZRS 儲存體帳戶。
- 您的儲存體帳戶是 premium 分頁 blob 或區塊 blob 帳戶。
- 您想要將資料從 ZRS 遷移至 LRS、GRS 或 RA-GRS。
- 您的儲存體帳戶包含封存層中的資料。

您可以透過 [Azure 支援入口網站](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)要求即時移轉。 從入口網站中，選取您想要轉換成 ZRS 的儲存體帳戶。

1. 選取 [ **新增支援要求**]。
2. 根據您的帳戶資訊完成 **基本概念** ： 
    - **問題類型**：選取 [ **技術**]。
    - **服務**：選取 [ **我的服務** 和 **儲存體帳戶管理**]。
    - **資源**：選取您想要轉換成 ZRS 的資源。
3. 選取 [下一步]。
4. 在 [問題] 區段中，指定下列值︰
    - **嚴重性**：將預設值保持不變。
    - **問題類型**：選取 [資料移轉]。
    - **類別**：選取 [ **遷移至 ZRS**]。
    - **標題**：輸入描述性標題，例如 **ZRS 帳戶移轉**。
    - **詳細資料**：在 [**詳細資料**] 方塊中輸入其他詳細資料，例如，我想要從區域中的 [LRS，GRS] 遷移至 ZRS \_ \_ 。
5. 選取 [下一步]。
6. 確認 [連絡人資訊] 刀鋒視窗上的連絡人資訊正確。
7. 選取 [建立]。

支援人員將會與您連絡，並提供您所需的一切協助。

> [!NOTE]
> Premium 檔案共用 (FileStorage 帳戶) 僅適用于 LRS 和 ZRS。
>
> GZRS 儲存體帳戶目前不支援封存層。 如需詳細資訊，請參閱 [Azure Blob 儲存體：經常性存取層、非經常性存取層和封存存取層](../blobs/storage-blob-storage-tiers.md) 。
>
> 受控磁片僅供 LRS 使用，無法遷移至 ZRS。 您可以在標準 HDD 儲存體上儲存標準 SSD 受控磁片的快照集和映射，並在 [LRS 和 ZRS 選項之間選擇](https://azure.microsoft.com/pricing/details/managed-disks/)。 如需與可用性設定組整合的詳細資訊，請參閱 [Azure 受控磁片簡介](../../virtual-machines/managed-disks-overview.md#integration-with-availability-sets)。

## <a name="switch-from-zrs-classic"></a>從傳統 ZRS 切換

> [!IMPORTANT]
> Microsoft 會在 2021 年 3 月 31 日將傳統 ZRS 帳戶除役並進行移轉。 在淘汰之前，會對傳統 ZRS 客戶提供更多詳細資料。
>
> ZRS 在指定的區域正式推出後，客戶將無法再從該區域中的 Azure 入口網站建立 ZRS 傳統帳戶。 在傳統 ZRS 被淘汰之前，可選擇使用 Microsoft PowerShell 和 Azure CLI 來建立傳統 ZRS 帳戶。 如需 ZRS 可用位置的相關資訊，請參閱 [Azure 儲存體冗余](storage-redundancy.md)。

傳統 ZRS 會以非同步方式在一至兩個區域內的資料中心間複寫資料。 除非 Microsoft 起始對次要區域的容錯移轉，否則複寫的資料可能無法供使用。 傳統 ZRS 帳戶無法以 LRS、GRS 或 RA-GRS 作為轉換目標或來源。 傳統 ZRS 帳戶也不支援計量或記錄。

傳統 ZRS 僅適用於一般用途 V1 (GPv1) 儲存體帳戶中的 **區塊 Blob**。 如需有關儲存體帳戶的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](storage-account-overview.md)。

若要手動將 ZRS 帳戶資料移轉至 LRS、GRS、RA GRS 或 ZRS 傳統帳戶，請使用下列其中一種工具： AzCopy、Azure 儲存體總管、PowerShell 或 Azure CLI。 您也可以使用其中一個 Azure 儲存體用戶端程式庫，自行建置移轉解決方案。

您也可以在可使用 ZRS 的區域中使用 Azure 入口網站、PowerShell 或 Azure CLI，將 ZRS 傳統儲存體帳戶升級至 ZRS。

# <a name="portal"></a>[入口網站](#tab/portal)

若要在 Azure 入口網站中升級至 ZRS，請流覽至帳戶 **的設定，然後選擇 [** **升級**]：

![在入口網站中將傳統 ZRS 升級至 ZRS](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 升級至 ZRS，請呼叫下列命令：

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 升級至 ZRS，請呼叫下列命令：

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>變更資料複寫方式的相關成本

變更資料複寫方式的相關成本取決於您的轉換路徑。 從至少到最昂貴的 Azure 儲存體冗余供應專案，包括 LRS、ZRS、GRS、RA-GRS、GZRS 和 RA-GZRS。

例如， *從* LRS 到任何其他類型的複寫都會產生額外的費用，因為您正在移至更精密的複製層級。 遷移 *至* GRS 或 GRS 將會產生輸出頻寬費用，因為您的主要區域) 中的資料 (會複寫到遠端次要區域。 這項費用是在初始設定時的一次性成本。 複製資料之後，將不會有任何進一步的遷移費用。 如需有關頻寬費用的詳細資料，請參閱 [Azure 儲存體定價頁面](https://azure.microsoft.com/pricing/details/storage/blobs/)找到。

如果您將儲存體帳戶從 GRS 遷移至 LRS，則不會產生額外費用，但會從次要位置刪除複寫的資料。

> [!IMPORTANT]
> 如果您將儲存體帳戶從 GRS 遷移至 GRS 或 LRS，則該帳戶會在其轉換日期之後的30天內以 RA-GRS 的方式計費。

## <a name="see-also"></a>另請參閱

- [Azure 儲存體複寫](storage-redundancy.md)
- [檢查儲存體帳戶的上次同步時間屬性](last-sync-time-get.md)
- [使用異地備援來設計高可用性的應用程式](geo-redundant-design.md)
