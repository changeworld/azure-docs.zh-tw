---
title: 建立具有異地區域冗余儲存體（切換）（預覽）的高可用性 Azure 儲存體應用程式 |Microsoft Docs
description: 異地區域冗余儲存體（切換）結婚區域冗余儲存體（ZRS）的高可用性，並防止異地多餘儲存體（GRS）所提供的區域性中斷。 切換儲存體帳戶中的資料會在主要區域中的 Azure 可用性區域之間複寫，同時也會複寫到次要地理區域，以防範區域性災難。
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: c102941920d2b8746a49be47ef4c5613fa0bc281
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719070"
---
# <a name="build-highly-available-azure-storage-applications-with-geo-zone-redundant-storage-gzrs-preview"></a>建立具有異地區域冗余儲存體（切換）（預覽）的高可用性 Azure 儲存體應用程式

異地區域冗余儲存體（切換）（預覽）結婚[區域冗余儲存體（ZRS）](storage-redundancy-zrs.md)的高可用性，並防止[異地多餘儲存體（GRS）](storage-redundancy-grs.md)所提供的區域性中斷。 切換儲存體帳戶中的資料會複寫到主要區域中的三個[Azure 可用性區域](../../availability-zones/az-overview.md)，也會複寫到次要地理區域，以保護不受區域性災難的影響。 每個 Azure 區域都會與相同地理位置內的另一個區域配對，以共同形成區域配對。 如需詳細資訊和例外狀況，請參閱[檔](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

透過切換儲存體帳戶，您可以在可用性區域無法使用或無法復原時，繼續讀取和寫入資料。 此外，如果發生全區域中斷或嚴重損壞而無法復原主要區域的情況，您的資料也會是持久的。 切換的設計目的是要在指定的一年內提供至少99.99999999999999% （16個9）的物件持久性。 切換也提供與 LRS、ZRS、GRS 或 RA-GRS 相同的擴充性目標。 如果您的應用程式需要能夠在主要區域發生嚴重損壞時讀取資料，您可以選擇性地使用讀取權限異地區域冗余儲存體（RA-切換）來啟用次要區域中資料的讀取權限。

Microsoft 建議針對需要一致性、持久性、高可用性、絕佳效能和復原的應用程式，使用切換來進行嚴重損壞修復。 如需在發生區域性嚴重損壞時，對次要區域進行讀取存取的額外安全性，請為您的儲存體帳戶啟用 [RA-切換]。

## <a name="about-the-preview"></a>關於預覽

只有一般用途 v2 儲存體帳戶支援切換和 RA 切換。 如需有關儲存體帳戶類型的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](storage-account-overview.md)。 切換和 RA-切換支援區塊 blob、分頁 blob （不是 VHD 磁片）、檔案、資料表和佇列。

切換和 RA-切換目前在下欄區域提供預覽：

- 東南亞
- 北歐
- 西歐
- 日本東部
- 英國南部
- 美國東部
- 美國東部 2
- 美國中部

Microsoft 會繼續在其他 Azure 區域中啟用切換和 RA 切換。 請定期查看[Azure 服務更新](https://azure.microsoft.com/updates/)頁面，以取得支援區域的相關資訊。

如需預覽定價的詳細[資訊，請](https://azure.microsoft.com/pricing/details/storage/files/)參閱[blob](https://azure.microsoft.com/pricing/details/storage/blobs)、檔案、[佇列](https://azure.microsoft.com/pricing/details/storage/queues/)和[資料表](https://azure.microsoft.com/pricing/details/storage/tables/)的切換預覽定價。

> [!IMPORTANT]
> Microsoft 建議不要針對生產工作負載使用預覽功能。

## <a name="how-gzrs-and-ra-gzrs-work"></a>切換和 RA 切換的工作方式

當資料寫入至已啟用切換或 RA 切換的儲存體帳戶時，該資料會先在主要區域中以同步方式跨三個可用性區域進行複寫。 然後，資料會以非同步方式複寫到相距數百英里的第二個區域。 當資料寫入次要區域時，會使用[本機多餘的儲存體（LRS）](storage-redundancy-lrs.md)，在該區域內以同步方式進一步複寫三次。

> [!IMPORTANT]
> 非同步複寫牽涉到將資料寫入主要區域，以及何時複寫到次要區域之間的延遲時間。 當發生區域性災害時，如果無法從主要區域復原尚未複寫到次要區域的變更，則這些變更可能會遺失。

當您建立儲存體帳戶時，可以指定要如何複寫該帳戶中的資料，而且您也可以指定該帳戶的主要區域。 異地複寫帳戶的配對次要區域是根據主要區域而定，且無法變更。 如需有關 Azure 所支援區域的最新資訊，請參閱[商務持續性和災害復原 (BCDR)：Azure 配對的區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。 如需使用切換或 RA-切換建立儲存體帳戶的相關資訊，請參閱[建立儲存體帳戶](storage-account-create.md)。

### <a name="use-ra-gzrs-for-high-availability"></a>使用 RA-切換來提供高可用性

當您啟用儲存體帳戶的切換時，您的資料可以從次要端點和儲存體帳戶的主要端點讀取。 次要端點會將尾碼 *（次要）* 附加至帳戶名稱。 例如，如果 Blob 服務的主要端點是 `myaccount.blob.core.windows.net`，則次要端點會是 `myaccount-secondary.blob.core.windows.net`。 主要和次要端點會有相同的儲存體帳戶存取金鑰。

若要在發生區域性中斷的情況下利用切換的功能，您必須事先設計應用程式來處理此案例。 您的應用程式應該讀取及寫入主要端點，但在主要區域無法使用的情況下，請切換為使用次要端點。 如需使用 RA 切換設計高可用性的指引，請參閱[使用 ra-切換或 RA-GRS 設計高度可用的應用程式](https://docs.microsoft.com/azure/storage/common/storage-designing-ha-apps-with-ragrs)。

由於資料會以非同步方式複寫到次要區域，因此次要區域通常會在主要區域後面。 若要判斷哪些寫入作業已複寫到次要區域，您的應用程式會檢查儲存體帳戶的上次同步處理時間。 在上次同步處理時間之前寫入主要區域的所有寫入作業，都已成功複寫到次要區域，這表示它們可以從次要資料庫讀取。 在上次同步處理時間之後寫入主要區域的任何寫入作業，可能會或可能尚未複寫到次要區域，這表示它們可能無法供讀取作業使用。

您可以使用 Azure PowerShell、Azure CLI 或其中一個 Azure 儲存體用戶端程式庫，查詢 [**上次同步時間**] 屬性的值。 [**上次同步時間**] 屬性是 GMT 日期/時間值。

如需有關切換的效能和擴充性的其他指引，請參閱[Microsoft Azure 儲存體效能和擴充性檢查清單](storage-performance-checklist.md)。

### <a name="availability-zone-outages"></a>可用性區域中斷

如果失敗影響主要區域中的可用性區域，您的應用程式可以使用該區域的其他可用性區域，順暢地繼續讀取和寫入您的儲存體帳戶。 Microsoft 建議您在使用切換或 ZRS 時，繼續遵循暫時性錯誤處理的作法。 這些做法包括實作具有指數輪詢的重試原則。

當可用性區域變得無法使用時，Azure 科學家網路更新，例如 DNS 重新指向。 如果您在更新完成之前存取資料，這些更新可能會影響您的應用程式。

### <a name="regional-outages"></a>區域中斷

如果失敗影響整個主要區域，則 Microsoft 會先嘗試還原主要區域。 如果無法還原，Microsoft 就會故障轉換到次要區域，讓次要區域成為新的主要區域。 如果儲存體帳戶已啟用切換，則針對此案例所設計的應用程式可以在等候容錯移轉時，從次要區域讀取。 如果儲存體帳戶未啟用切換，則在容錯移轉完成之前，應用程式將無法讀取次要資料庫。

> [!NOTE]
> 切換和 RA-切換目前僅在美國東部地區處於預覽狀態。 美國東部2尚未提供客戶管理的帳戶容錯移轉（預覽），因此客戶目前無法使用切換和 RA 切換帳戶來管理帳戶容錯移轉事件。 在預覽期間，Microsoft 將會管理影響切換和 RA 切換帳戶的任何容錯移轉事件。

由於資料會以非同步方式複寫到次要區域，因此如果無法復原主要區域，則會影響主要區域的失敗可能會導致資料遺失。 最近寫入主要區域和上次寫入次要區域之間的間隔，稱為復原點目標（RPO）。 RPO 表示可復原資料的時間點。 Azure 儲存體通常會有不到15分鐘的 RPO，但目前並沒有將資料複寫到次要區域所需時間的 SLA。

復原時間目標（RTO）是一種測量執行容錯移轉和讓儲存體帳戶恢復上線所需的時間。 此量值會藉由變更主要 DNS 專案以指向次要位置，來指出 Azure 執行容錯移轉所需的時間。

## <a name="migrate-a-storage-account-to-gzrs-or-ra-gzrs"></a>將儲存體帳戶遷移至切換或 RA-切換

您可以將任何現有的儲存體帳戶遷移至切換或 RA-切換。 從現有的 ZRS 帳戶遷移至切換或 RA-切換相當簡單，而從 LRS、GRS 或 RA-GRS 帳戶進行遷移比較複雜。 下列各節說明如何在任一情況下遷移。

**已知的限制**

- （RA-）切換帳戶目前不支援封存層。 如需詳細資訊，請參閱[Azure Blob 儲存體：經常性存取、非經常性存取和封存存取層](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)。
- 受控磁片不支援（RA-）切換。 您可以在標準 HDD 儲存體上儲存標準 SSD 受控磁碟的快照集和影像，並在[LRS 和 ZRS 選項之間進行選擇](https://azure.microsoft.com/pricing/details/managed-disks/)。

### <a name="migrating-from-a-zrs-account"></a>從 ZRS 帳戶進行遷移

若要將現有的 ZRS 帳戶轉換為切換，請使用[new-azstorageaccount](/powershell/module/az.storage/set-azstorageaccount) Cmdlet 來變更帳戶的 SKU。 請記得使用您自己的值來取代預留位置值：

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -SkuName "Standard_RAGZRS"
```

### <a name="migrating-from-an-lrs-grs-or-ra-grs-account"></a>從 LRS、GRS 或 RA GRS 帳戶進行遷移

有兩個選項可從 LRS、GRS 或 RA-GRS 帳戶遷移至切換或 RA-切換：

- 您可以從現有的帳戶手動將資料複製或移動到新的切換或 RA 切換帳戶。
- 您可以要求即時移轉。

#### <a name="perform-a-manual-migration"></a>執行手動遷移

如果您需要以特定日期完成遷移，請考慮執行手動遷移。 手動移轉比即時移轉更具彈性。 使用手動移轉時，您可以控制時間。

若要手動將資料從現有的帳戶遷移到切換或 RA 切換帳戶，請使用可有效率地複製資料的工具。 部分範例包括：

- 使用 AzCopy 或可靠的協力廠商工具之類的公用程式。 如需 AzCopy 的相關資訊，請參閱[開始使用 AzCopy](storage-use-azcopy-v10.md)。
- 如果您熟悉 Hadoop 或 HDInsight，請將來源和目的地儲存體帳戶連結到您的叢集。 接下來，使用 DistCp 之類的工具來平行處理資料複製程式。
- 使用其中一個「Azure 儲存體」用戶端程式庫來建置您自己的工具。

#### <a name="perform-a-live-migration"></a>執行即時移轉

手動移轉可能造成應用程式停機。 如果您的應用程式要求高可用性，Microsoft 也提供即時移轉選項。 即時移轉是一種不需停機的就地移轉。

在即時移轉期間，您可以在來源和目的地儲存體帳戶之間遷移資料時，使用您的儲存體帳戶。 在即時移轉過程中，您的帳戶會繼續符合其持久性和可用性的 SLA。 即時移轉不會造成停機或資料遺失。

只有一般用途 v2 帳戶支援切換/RA-切換，因此在提交即時移轉至切換/RA 切換的要求之前，您必須將帳戶升級為一般用途 v2。 如需詳細資訊，請參閱[Azure 儲存體帳戶總覽](https://docs.microsoft.com/azure/storage/common/storage-account-overview)和[升級至一般用途 v2 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)。

完成遷移之後，儲存體帳戶的複寫設定會更新為**異地區域冗余儲存體（切換）** 或**讀取權限異地區域-多餘儲存體（RA-切換）** 。 服務端點、存取金鑰、共用存取簽章（SAS）和任何其他帳戶設定選項會保持不變且原封不動。

請記住下列即時移轉限制：

- 雖然 Microsoft 會迅速處理您的即時移轉要求，但不保證移轉何時會完成。 如果您需要依特定日期遷移至切換或 RA 切換的資料，Microsoft 建議您改為執行手動遷移。 一般而言，您帳戶中的資料越多，移轉該資料所需的時間就越長。
- 只有使用 GRS 或 RA GRS 複寫的儲存體帳戶才支援即時移轉。 如果您的帳戶使用 LRS，您必須先將帳戶的複寫類型變更為 GRS 或 RA-GRS，才能繼續進行。 這個媒介步驟會新增 GRS/RA-GRS 所提供的次要端點。
- 您的帳戶必須包含資料。
- 您只能在相同的區域內移轉資料。
- 只有標準儲存體帳戶類型支援即時移轉。 進階儲存體帳戶必須以手動方式移轉。
- 不支援從切換或 RA 切換帳戶即時移轉至 LRS、GRS 或 RA-GRS 帳戶。 您必須手動將資料移至新的或現有的儲存體帳戶。
- 您可以要求從 GRS 到 RA-切換的即時移轉。 不過，不支援從 RA-GRS 遷移至切換。 在此情況下，您必須要求即時移轉至切換，然後以手動方式將儲存體帳戶轉換為使用切換。
- 受控磁片僅支援 LRS，且無法遷移至切換或 RA-切換。 如需與可用性設定組整合的詳細說明，請參閱[Azure 受控磁片簡介](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)。
- 您可以在標準 HDD 儲存體上儲存標準 SSD 受控磁碟的快照集和映射，並在[LRS、ZRS、切換和 RA 切換選項之間進行選擇](https://azure.microsoft.com/pricing/details/managed-disks/)。
- 切換不支援包含大型檔案共用的帳戶。

若要要求即時移轉，請使用[Azure 入口網站](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 從入口網站中，選取要遷移至切換或 RA 切換的儲存體帳戶，並遵循下列指示：

1. 選取 [**新增支援要求**]。
2. 根據您的帳戶資訊，完成 [基本] 資訊。 在 [**服務**] 區段中，選取 [**儲存體帳戶管理**]，並指定要遷移的帳戶。
3. 選取 [下一步]。
4. 在 [問題] 區段中，指定下列值︰
    - **嚴重性**：將預設值保持不變。
    - **問題類型**：選取 [資料移轉]。
    - **類別**：選取**區域內的 [遷移至（RA-）切換**]。
    - **標題**：輸入描述性標題，例如 **（RA-）切換帳戶遷移**。
    - **詳細資料**：在 [**詳細資料**] 方塊中輸入其他詳細資料，例如，在 \_\_ 區域中的 [我要從 [LRS，GRS] 遷移至切換]。 或者，我想要從 \_\_ 區域中的 [LRS，RA-GRS] 遷移至 [切換]。
5. 選取 [下一步]。
6. 確認 [連絡人資訊] 刀鋒視窗上的連絡人資訊正確。
7. 選取 [建立]。

支援代表會聯絡您以提供協助。

## <a name="see-also"></a>另請參閱

- [Azure 儲存體複寫](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
- [本地備援儲存體 (LRS)：適用於 Azure 儲存體的低成本資料備援](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)
- [區域備援儲存體 (ZRS)：高可用性 Azure 儲存體應用程式](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) 
- [標準儲存體帳戶的延展性和效能目標](scalability-targets-standard-account.md)
