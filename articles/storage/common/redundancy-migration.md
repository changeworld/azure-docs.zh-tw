---
title: 更改存儲帳戶的複製方式
titleSuffix: Azure Storage
description: 瞭解如何更改現有存儲帳戶中的資料複製方式。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 5c37dbdc34138faab8adae6ad18252c18a75cad4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337082"
---
# <a name="change-how-a-storage-account-is-replicated"></a>更改存儲帳戶的複製方式

Azure 存儲始終存儲資料的多個副本，以便防止其免受計畫內和計畫外事件的影響，包括暫態硬體故障、網路或斷電以及大規模自然災害。 冗余可確保存儲帳戶符合 Azure[存儲的服務等級協定 （SLA），](https://azure.microsoft.com/support/legal/sla/storage/)即使在發生故障時也是如此。

Azure 存儲提供以下類型的複製：

- 本機備援儲存體 (LRS)
- 區域備援儲存體 (ZRS)
- 異地冗余存儲 （GRS） 或讀取存取異地冗余存儲 （RA-GRS）
- 地理區域冗余存儲 （GZRS） 或讀取存取地理區域冗余存儲 （RA-GZRS）（預覽）

有關每個選項的概述，請參閱 Azure[存儲冗余](storage-redundancy.md)。

## <a name="switch-between-types-of-replication"></a>在複製類型之間切換

您可以將存儲帳戶從一種類型的複製切換到任何其他類型，但某些方案比其他方案更直接。 如果要添加或刪除對次要區域的異地複製或讀取存取，可以使用 Azure 門戶、PowerShell 或 Azure CLI 更新複製設置。 但是，如果要更改在主區域中複製資料的方式，通過從 LRS 移動到 ZRS，反之亦然，則必須執行手動遷移。

下表概述了如何從每種複製類型切換到另一種類型的複製：

| 開關 | ...到 LRS | ...到 GRS/RA-GRS | ...到 ZRS | ...到 GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>...從 LRS</b> | N/A | 使用 Azure 門戶、PowerShell 或 CLI 更改複製設置<sup>1</sup> | 執行手動遷移 <br /><br />請求即時移轉 | 執行手動遷移 <br /><br /> OR <br /><br /> 先切換到 GRS/RA-GRS，然後請求即時移轉<sup>1</sup> |
| <b>...從 GRS/RA-GRS</b> | 使用 Azure 門戶、PowerShell 或 CLI 更改複製設置 | N/A | 執行手動遷移 <br /><br /> OR <br /><br /> 先切換到 LRS，然後請求即時移轉 | 執行手動遷移 <br /><br /> 請求即時移轉 |
| <b>...從 ZRS</b> | 執行手動遷移 | 執行手動遷移 | N/A | 使用 Azure 門戶、PowerShell 或 CLI 更改複製設置<sup>1</sup> |
| <b>...從 GZRS/RA-GZRS</b> | 執行手動遷移 | 執行手動遷移 | 使用 Azure 門戶、PowerShell 或 CLI 更改複製設置 | N/A |

<sup>1</sup>產生一次性出口費用。

> [!CAUTION]
> 如果對 （RA-） GRS 或 （RA-） GZRS 帳戶執行[帳戶容錯移轉](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance)，則新主區域中將其配置為本地冗余。 不支援此類 LRS 帳戶的即時移轉到 ZRS 或 GZRS。 您將需要執行[手動遷移](https://docs.microsoft.com/azure/storage/common/redundancy-migration#perform-a-manual-migration-to-zrs)。

## <a name="change-the-replication-setting"></a>更改複製設置

只要未更改在主區域中複製資料的方式，可以使用 Azure 門戶、PowerShell 或 Azure CLI 更改存儲帳戶的複製設置。 如果要從主區域中的 LRS 遷移到主區域中的 ZRS，反之亦然，則必須執行[手動遷移](#perform-a-manual-migration-to-zrs)或[即時移轉](#request-a-live-migration-to-zrs)。

更改存儲帳戶的複製方式不會導致應用程式的停機時間。

# <a name="portal"></a>[入口網站](#tab/portal)

要更改 Azure 門戶中的存儲帳戶的冗余選項，請按照以下步驟操作：

1. 在 Azure 入口網站中巡覽至您的儲存體帳戶。
1. 選擇 **"配置"** 設置。
1. 更新**複製**設置。

![顯示如何在門戶中更改複製選項的螢幕截圖](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[電源外殼](#tab/powershell)

要使用 PowerShell 更改存儲帳戶的冗余選項，請調用[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)命令並`-SkuName`指定參數：

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要使用 Azure CLI 更改存儲帳戶的冗余選項，請調用[az 存儲帳戶更新](/cli/azure/storage/account#az-storage-account-update)命令`--sku`並指定參數：

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs"></a>執行手動遷移到 ZRS

如果要更改存儲帳戶中的資料在主區域中的複製方式，通過從 LRS 移動到 ZRS，反之亦然，則可以選擇執行手動遷移。 手動移轉比即時移轉更具彈性。 您可以控制手動遷移的時間，因此，如果您需要遷移以在特定日期完成，請使用此選項。

當您在主區域中執行從 LRS 到 ZRS 的手動遷移時，目標存儲帳戶可以是異地冗余的，也可以配置為對次要區域的讀取存取。 例如，您可以一步將 LRS 帳戶遷移到 GZRS 或 RA-GZRS 帳戶。

手動移轉可能造成應用程式停機。 如果您的應用程式要求高可用性，Microsoft 也提供即時移轉選項。 即時移轉是一種不需停機的就地移轉。

通過手動遷移，可以將資料從現有存儲帳戶複製到在主區域中使用 ZRS 的新存儲帳戶。 要執行手動遷移，可以使用以下選項之一：

- 使用現有工具（如 AzCopy、Azure 存儲用戶端庫之一或可靠的協力廠商工具）複製資料。
- 如果您熟悉 Hadoop 或 HDInsight，則可以將源存儲帳戶和目標存儲帳戶附加到群集。 然後，使用 DistCp 之類的工具來平行處理資料複製程序。

## <a name="request-a-live-migration-to-zrs"></a>請求即時移轉到 ZRS

如果需要將存儲帳戶從 LRS 或 GRS 遷移到主區域中的 ZRS，而沒有應用程式停機，則可以請求 Microsoft 進行即時移轉。 在即時移轉期間，您可以訪問存儲帳戶中的資料，並且不會丟失持久性或可用性。 Azure 存儲 SLA 在遷移過程中維護。 沒有與即時移轉關聯的資料丟失。 遷移後，服務終結點、訪問金鑰、共用訪問簽名和其他帳戶選項保持不變。

ZRS 僅支援通用 v2 帳戶，因此請確保在提交即時移轉到 ZRS 的請求之前升級存儲帳戶。 有關詳細資訊，請參閱[升級到通用 v2 存儲帳戶](storage-account-upgrade.md)。 存儲帳戶必須包含要通過即時移轉遷移的資料。

只有針對使用 LRS 或 GRS 複寫的儲存體帳戶，才支援即時移轉。 如果您的帳戶使用 RA-GRS，則必須先將帳戶複寫類型變更為 LRS 或 GRS，再繼續進行操作。 這個中繼步驟會在移轉之前，先將 RA-GRS 所提供的次要唯讀端點移除。

雖然 Microsoft 會迅速處理您的即時移轉要求，但不保證移轉何時會完成。 如果您需要在特定日期之前將資料移轉至 ZRS，則 Microsoft 建議您改為執行手動移轉。 一般而言，您帳戶中的資料越多，移轉該資料所需的時間就越長。

您必須執行手動遷移，如果：

- 您希望將資料移轉到位於與源帳戶不同的區域中的 ZRS 存儲帳戶。
- 您的存儲帳戶是高級頁面 blob 或塊 Blob 帳戶。
- 您希望將資料從 ZRS 遷移到 LRS、GRS 或 RA-GRS。
- 您的存儲帳戶包括存檔層中的資料。

您可以透過 [Azure 支援入口網站](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)要求即時移轉。 從入口網站中，選取您想要轉換成 ZRS 的儲存體帳戶。

1. 選擇**新的支援請求**
2. 根據您的帳戶資訊，完成 [基本]**** 資訊。 在 [服務]**** 區段中，選取 [儲存體帳戶管理]**** 和您想要轉換成 ZRS 的資源。
3. 選取 [下一步]****。
4. 在 [問題]**** 區段中，指定下列值︰
    - **嚴重性**：將預設值保持不變。
    - **問題類型**：選取 [資料移轉]****。
    - **類別**： 選擇**遷移到 ZRS**。
    - **標題**：輸入描述性標題，例如 **ZRS 帳戶移轉**。
    - **詳細資訊**：在 **"詳細資訊"** 框中鍵入其他詳細資訊，例如，我想從\_\_該區域的 [LRS， GRS] 遷移到 ZRS。
5. 選取 [下一步]****。
6. 確認 [連絡人資訊]**** 刀鋒視窗上的連絡人資訊正確。
7. 選取 [建立]****。

支援人員將會與您連絡，並提供您所需的一切協助。

> [!NOTE]
> 高級檔共用當前不支援即時移轉。 目前僅支援手動複製或移動資料。
>
> GZRS 存儲帳戶當前不支援存檔層。 有關詳細資訊[，請參閱 Azure Blob 存儲：熱、酷和存檔訪問層](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)。
>
> 託管磁片僅適用于 LRS，不能遷移到 ZRS。 您可以將標準 SSD 託管磁片的快照和映射存儲在標準硬碟存儲上，並在[LRS 和 ZRS 選項之間進行選擇](https://azure.microsoft.com/pricing/details/managed-disks/)。 有關與可用性集集成的資訊，請參閱[Azure 託管磁片簡介](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)。

## <a name="switch-from-zrs-classic"></a>從 ZRS 經典切換

> [!IMPORTANT]
> Microsoft 會在 2021 年 3 月 31 日將傳統 ZRS 帳戶除役並進行移轉。 在淘汰之前，會對傳統 ZRS 客戶提供更多詳細資料。
>
> ZRS 在給定區域中普遍可用後，客戶將無法再從該區域的 Azure 門戶創建 ZRS 經典帳戶。 在傳統 ZRS 被淘汰之前，可選擇使用 Microsoft PowerShell 和 Azure CLI 來建立傳統 ZRS 帳戶。 有關 ZRS 可用位置的資訊，請參閱[Azure 存儲冗余](storage-redundancy.md)。

傳統 ZRS 會以非同步方式在一至兩個區域內的資料中心間複寫資料。 除非 Microsoft 起始對次要區域的容錯移轉，否則複寫的資料可能無法供使用。 傳統 ZRS 帳戶無法以 LRS、GRS 或 RA-GRS 作為轉換目標或來源。 傳統 ZRS 帳戶也不支援計量或記錄。

傳統 ZRS 僅適用於一般用途 V1 (GPv1) 儲存體帳戶中的**區塊 Blob**。 如需有關儲存體帳戶的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](storage-account-overview.md)。

要手動將 ZRS 帳戶資料移轉到 LRS、GRS、RA-GRS 或 ZRS 經典帳戶，請使用以下工具之一：AzCopy、Azure 存儲資源管理器、PowerShell 或 Azure CLI。 您也可以使用其中一個 Azure 儲存體用戶端程式庫，自行建置移轉解決方案。

您還可以在 ZRS 可用的區域中使用 Azure 門戶、PowerShell 或 Azure CLI 將 ZRS 經典存儲帳戶升級到 ZRS。

# <a name="portal"></a>[入口網站](#tab/portal)

要在 Azure 門戶中升級到 ZRS，請導航到帳戶的 **"配置"** 設置，然後選擇 **"升級**" ：

![在門戶中將 ZRS 經典升級到 ZRS](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[電源外殼](#tab/powershell)

要使用 PowerShell 升級到 ZRS，請調用以下命令：

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要使用 Azure CLI 升級到 ZRS，請調用以下命令：

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>與更改資料複製方式相關的成本

與更改資料複製方式相關的成本取決於轉化路徑。 從最少到最昂貴的 Azure 存儲冗余產品的順序包括 LRS、ZRS、GRS、RA-GRS、GZRS 和 RA-GZRS。

例如，*從*LRS 到任何其他類型的複製將會產生額外費用，因為您將移動到更複雜的冗余級別。 *遷移到*GRS 或 RA-GRS 將會產生出口頻寬費用，因為您的資料（在主區域中）正在複製到遠端次要區域。 這筆費用是初始設置的一次性成本。 複製資料後，無需支付進一步的遷移費用。 如需有關頻寬費用的詳細資料，請參閱 [Azure 儲存體定價頁面](https://azure.microsoft.com/pricing/details/storage/blobs/)找到。

如果將存儲帳戶從 GRS 遷移到 LRS，則無需支付額外費用，但複製的資料將從輔助位置刪除。

> [!IMPORTANT]
> 如果將存儲帳戶從 RA-GRS 遷移到 GRS 或 LRS，則該帳戶在轉換之日起 30 天內將計費為 RA-GRS。

## <a name="see-also"></a>另請參閱

- [Azure 存儲冗余](storage-redundancy.md)
- [檢查存儲帳戶的"上次同步時間"屬性](last-sync-time-get.md)
- [使用讀取存取異地冗余存儲設計高可用應用程式](storage-designing-ha-apps-with-ragrs.md)
