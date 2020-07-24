---
title: 資料備援
titleSuffix: Azure Storage
description: 系統會 複製Microsoft Azure 儲存體帳戶中的資料，以維持持久性和高可用性。 備援設定包含本地備援儲存體 (LRS)、區域備援儲存體 (ZRS)、異地備援儲存體 (GRS)、讀取權限異地備援儲存體 (RA-GRS)、異地區域備援儲存體 (GZRS)，以及讀取權限異地區域備援儲存體 (RA-GZRS)。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/21/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: e4ec4925da40cf6051b88d77fbbc35d93ececf87
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87036721"
---
# <a name="azure-storage-redundancy"></a>Azure 儲存體備援

Azure 儲存體會一律儲存資料的多個複本，以保護該資料不受計劃性和非計劃性事件影響，包括暫時性硬體故障、網路或電力中斷和大規模天然災害。 備援可確保儲存體帳戶符合 [Azure 儲存體的服務等級協定 (SLA)](https://azure.microsoft.com/support/legal/sla/storage/)，即使遇到失敗時也一樣。

在決定哪一種備援選項最適合案例時，請考慮較低成本與較高可用性和持久性之間的取捨。 有助於判斷應選擇何種備援選項的因素包括：  

- 資料在主要區域中複寫的方式
- 資料是否會複寫到地理上距離主要區域相當遙遠的第二個位置，以保護其不受區域性災害影響
- 若主要區域因任何理由而無法使用，則應用程式是否需要讀取存取來複寫次要區域中的資料

## <a name="redundancy-in-the-primary-region"></a>主要區域中的備援

Azure 儲存體帳戶中的資料一律會在主要區域內複寫三次。 Azure 儲存體提供兩個選項以供決定資料在主要區域中的複寫方式：

- **本地備援儲存體 (LRS)** 會在主要區域中將資料於單一實體位置內同步複製三次。 LRS 是成本最低的複寫選項，但不建議用於需要高可用性的應用程式。
- **區域備援儲存體 (ZRS)** 會在主要區域中將資料同步複製到三個 Azure 可用區域。 針對需要高可用性的應用程式，Microsoft 建議在主要區域中使用 ZRS，並複寫至次要區域。

### <a name="locally-redundant-storage"></a>本地備援儲存體

本地備援儲存體 (LRS) 會在主要區域中將資料於單一實體位置內複寫三次。 LRS 可提供在指定一年中至少 99.999999999% (11 個九) 的物件持久性。

相較於其他選項，LRS 是成本最低的備援選項，且提供的持久性最弱。 LRS 可保護資料以避免伺服器機架和磁碟機失敗。 但是，若在資料中心內發生火災或洪水之類的災害，則所有使用 LRS 的儲存體帳戶複本可能都會遺失或無法復原。 若要降低此風險，Microsoft 建議使用[區域備援儲存體](#zone-redundant-storage) (ZRS)、[異地備援儲存體](#geo-redundant-storage) (GRS)，或[異地區域備援儲存體](#geo-zone-redundant-storage) (GZRS)。

對使用 LRS 儲存體帳戶發出的寫入要求會以同步方式進行。 只有在將資料寫入三個複本之後，寫入作業才會成功傳回。

針對下列案例，LRS 是良好的選擇：

- 如果您的應用程式儲存的資料，可在發生資料遺失時輕鬆重新建構，則您可以選擇使用 LRS。
- 若應用程式由於資料控管需求而只能在國家或地區中複寫資料，則可選擇 LRS。 在某些情況下，資料進行異地複寫的配對區域可能會位於另一個國家或地區中。 如需配對區域的詳細資訊，請參閱 [Azure 區域](https://azure.microsoft.com/regions/)。

### <a name="zone-redundant-storage"></a>區域備援儲存體

區域備援儲存體 (ZRS) 會在主要區域中將 Azure 儲存體資料同步複寫到三個 Azure 可用區域。 每個可用區域都是具備獨立的電源、冷卻和網路的不同實體位置。 ZRS 可在指定的一年中為 Azure 儲存體資料物件提供至少 99.9999999999% (12 個 9) 的持久性。

透過 ZRS，即使區域無法使用，您仍然可針對讀取和寫入作業存取資料。 若區域無法使用，則 Azure 會執行網路更新，例如 DNS 重新指向。 若在更新完成之前存取資料，這些更新便可能會影響應用程式。 在為 ZRS 設計應用程式時，請遵循暫時性錯誤處理實務，包括以指數後退法實作重試原則。

對使用 ZRS 儲存體帳戶發出的寫入要求會以同步方式進行。 只有在將資料寫入三個可用區域的所有複本之後，寫入作業才會成功傳回。

針對需要一致性、持久性和高可用性的案例，Microsoft 建議在主要區域中使用 ZRS。 ZRS 可為資料提供優秀的效能、低延遲以及復原，即使其暫時無法使用也一樣。 但是，ZRS 本身可能無法保護資料不受永久影響多個區域的區域性災害影響。 針對區域性災害的保護，Microsoft 建議使用[異地區域備援儲存體](#geo-zone-redundant-storage) (GZRS)，這種儲存體會在主要區域內使用 ZRS，但也會將資料異地複寫至次要區域。

下表顯示哪些儲存體帳戶類型在哪些區域支援 ZRS：

| 儲存體帳戶類型 | 支援區域 | 支援的服務 |
|--|--|--|
| 一般用途 V2<sup>1</sup> | 東南亞<br /> 澳大利亞東部<br /> 北歐<br />  西歐<br /> 法國中部<br /> 日本東部<br /> 南非北部<br /> 英國南部<br /> 美國中部<br /> 美國東部<br /> 美國東部 2<br /> 美國西部 2 | 區塊 Blob<br /> 分頁 blob<sup>2</sup><br /> 檔案共用 (標準)<br /> 資料表<br /> 佇列<br /> |
| BlockBlobStorage<sup>1</sup> | 東南亞<br /> 澳大利亞東部<br /> 西歐<br /> 美國東部 | 僅限 Premium 區塊 blob |
| FileStorage | 東南亞<br /> 澳大利亞東部<br /> 西歐<br /> 美國東部 | 僅限 Premium 檔案共用 |

<sup>1</sup> ZRS 帳戶目前不支援封存層。<br />
<sup>2</sup> 包含虛擬機器 Azure 受控磁碟的儲存體帳戶一律會使用 LRS。 Azure 非受控磁碟也應使用 LRS。 您可為使用 GRS 的 Azure 非受控磁碟建立儲存體帳戶，但不建議這種做法，因為可能會有非同步異地複寫所引發的潛在一致性問題。 受控或非受控磁碟都不支援 ZRS 或 GZRS。 如需受控磁碟的詳細資訊，請參閱 [Azure 受控磁碟定價](https://azure.microsoft.com/pricing/details/managed-disks/)。

如需支援 ZRS 區域的資訊，請參閱[什麼是可用區域？](../../availability-zones/az-overview.md)中的**服務支援 (依區域)** 。

## <a name="redundancy-in-a-secondary-region"></a>次要區域中的備援

針對需要高可用性的應用程式，可選擇另外將儲存體帳戶中資料複製到位於主要區域數百英哩外的次要區域。 若儲存體帳戶已複製到次要區域，則即使主要區域發生全區中斷或災害而無法復原的狀況，資料仍然會是耐久的。

建立儲存體帳戶時，您可以為帳戶選取主要區域。 配對的次要區域會視主要區域而定，且無法變更。 如需 Azure 支援區域的詳細資訊，請參閱 [Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)。

Azure 儲存體提供兩種將資料複製到次要區域的選項：

- **異地備援儲存體 (GRS)** 會使用 LRS 在主要區域中將資料於單一實體位置內同步複製三次。 接著其會將資料以非同步方式複製到次要區域中的單一實體位置。
- **異地區域備援儲存體 (GZRS)** 會使用 ZRS 在主要區域中將資料同步複製到三個 Azure 可用區域。 接著其會將資料以非同步方式複製到次要區域中的單一實體位置。

GRS 和 GZRS 之間主要差異是資料在主要區域中複寫的方式。 在次要位置內，資料一律會使用 LRS 以同步方式複寫三次。 次要區域中的 LRS 可保護您的資料免于硬體故障。

使用 GRS 或 GZRS，除非發生容錯移轉至次要區域，否則次要位置中的資料不會提供讀取或寫入存取。 如需次要位置的讀取存取，請設定儲存體帳戶使用讀取權限異地備援儲存體 (RA-GRS) 或讀取權限異地區域備援儲存體 (RS-GZRS)。 如需詳細資訊，請參閱[次要區域中的資料讀取存取](#read-access-to-data-in-the-secondary-region)。

若主要區域無法使用，則可選擇容錯移轉至次要區域。 在容錯移轉完成後，次要區域即會成為主要區域，且可再次讀取和寫入資料。 如需災害復原及了解如何容錯移轉至次要區域的詳細資訊，請參閱[災害復原與儲存體帳戶容錯移轉](storage-disaster-recovery-guidance.md)。

> [!IMPORTANT]
> 因為資料會以非同步方式複寫至次要區域，若主要區域無法復原，則影響主要區域的失敗可能會導致資料遺失。 最近寫入至主要區域及最後寫入次要區域之間的間隔稱為復原點目標 (RPO)。 RPO 表示可復原資料的時間點。 Azure 儲存體的 RPO 通常低於 15 分鐘，但目前並沒有關於將資料複寫至次要區域所花費時間的 SLA。

### <a name="geo-redundant-storage"></a>異地備援儲存體

異地備援儲存體 (GRS) 會使用 LRS 在主要區域中將資料於單一實體位置內同步複製三次。 其接著會將資料以非同步方式複製到位於主要區域數百英哩外次要區域中的單一實體位置。 GRS 可在指定一年中，為 Azure 儲存體資料物件提供至少 99.99999999999999% (16 個 9) 的持久性。

寫入作業會先認可到主要位置，並使用 LRS 進行複寫。 更新接著會以非同步方式複寫到次要區域。 當資料寫入次要位置時，也會使用 LRS 在該位置中複寫。

### <a name="geo-zone-redundant-storage"></a>異地區域備援儲存體

異地區域備援儲存體 (GZRS) 合併跨可用區域備援所提供的高可用性，以及異地複寫針對區域中斷所提供的保護。 GZRS 儲存體帳戶中資料會複製到主要區域中的三個 [Azure 可用區域](../../availability-zones/az-overview.md)，此外也會複寫到次要地理區域以保護其不受區域災害影響。 Microsoft 建議針對需要最大一致性、持久性、可用性、絕佳效能，以及針對災害復原恢復的應用程式使用 GZRS。

透過 GZRS 儲存體帳戶，若可用區域無法使用或無法復原，則仍可繼續讀取和寫入資料。 此外，資料也會在發生全區中斷或災害，而導致主要區域無法復原時維持耐久。 GZRS 旨在於指定的一年中，為物件提供至少 99.99999999999999% (16 個 9) 的持久性。

只有一般用途 V2 儲存體帳戶支援 GZRS 和 RA-GZRS。 如需有關儲存體帳戶類型的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](storage-account-overview.md)。 GZRS 和 RA-GZRS 支援區塊 blob、分頁 blob (VHD 磁碟除外)、檔案、資料表和佇列。

下列區域支援 GZRS 和 RA-GZRS：

- 東南亞
- 北歐
- 西歐
- 日本東部
- 英國南部
- 美國中部
- 美國東部
- 美國東部 2
- 美國西部 2

如需定價的詳細資訊，請參閱 [Blob](https://azure.microsoft.com/pricing/details/storage/blobs)、[檔案](https://azure.microsoft.com/pricing/details/storage/files/)、[佇列](https://azure.microsoft.com/pricing/details/storage/queues/)及[資料表](https://azure.microsoft.com/pricing/details/storage/tables/)的定價詳細資料。

## <a name="read-access-to-data-in-the-secondary-region"></a>次要區域中的資料讀取存取

異地備援儲存體 (使用 GRS 或 GZRS) 會將資料複寫到次要區域中的另一個實體位置，以保護其不受區域性中斷影響。 但是，該資料只有在客戶或 Microsoft 起始從主要區域容錯移轉至次要區域時，才能提供存取。 當您啟用次要區域的讀取權限時，您的資料隨時都可供讀取，包括主要區域變成無法使用的情況。 如需次要區域的讀取存取，請啟用讀取存取異地備援儲存體 (RA-GRS) 或讀取存取異地區域備援儲存體 (RA-GZRS)。

> [!NOTE]
> Azure 檔案儲存體不支援讀取權限異地冗余儲存體（RA-GRS）和讀取權限異地區域冗余儲存體（RA-切換）。

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>針對次要區域的讀取存取設計應用程式

若您已針對次要區域的讀取存取設定儲存體帳戶，即可設計應用程式，使其在主要區域因任何理由而無法使用時，完美地移動到從次要區域讀取資料。 

啟用 GRS 或 RA-切換之後，次要地區可供讀取存取，讓您可以事先測試應用程式，以確保在發生中斷的情況下，它會在次要複本中正確地讀取。 如需如何針對高可用性來設計應用程式的詳細資訊，請參閱[使用異地備援來設計高可用性應用程式](geo-redundant-design.md)。

啟用次要複本的讀取存取權時，您的應用程式可以從次要端點，以及從主要端點讀取。 次要端點會將 *–secondary* 尾碼附加到目前的帳戶名稱。 例如，若 Blob 儲存體主要端點是 `myaccount.blob.core.windows.net`，則次要端點便會是 `myaccount-secondary.blob.core.windows.net`。 主要和次要端點的儲存體帳戶帳戶存取金鑰為相同。

### <a name="check-the-last-sync-time-property"></a>檢查上次同步時間屬性

由於資料會以非同步方式複寫至次要區域，因此次要區域通常會位於主要區域之後。 若主要區域中發生失敗，則對主要區域進行的所有寫入很有可能尚未複寫到次要區域。

若要判斷哪些寫入作業已複寫到次要區域，應用程式可檢查儲存體帳戶的**上次同步時間**屬性。 所有在上次同步時間之前對主要區域進行的寫入作業都已成功複寫到次要區域，而這表示可從次要區域讀取這些項目。 任何在上次同步時間之後對主要區域進行的寫入作業可能，也可能尚未複寫到次要區域，而這表示您可能對其進行讀取作業。

您可使用 Azure PowerShell、Azure CLI 或其中一個 Azure 儲存體用戶端程式庫來查詢**上次同步時間**的值。 **上次同步時間**屬性是 GMT 日期/時間值。 如需詳細資訊，請參閱[檢查儲存體帳戶的上次同步時間屬性](last-sync-time-get.md)。

## <a name="summary-of-redundancy-options"></a>備援選項摘要

下列各節中的表格會摘要說明可供 Azure 儲存體使用的備援選項

### <a name="durability-and-availability-parameters"></a>持久性和可用性參數

下表描述每個備援選項的關鍵參數：

| 參數 | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| 指定一年中物件的持久性百分比<sup>1</sup> | 至少 99.999999999% (11 個 9) | 至少 99.9999999999% (12 個 9) | 至少 99.99999999999999% (16 個 9) | 至少 99.99999999999999% (16 個 9) |
| 讀取要求的可用性 SLA<sup>1</sup> | 至少 99.9% (非經常性存取層為 99%) | 至少 99.9% (非經常性存取層為 99%) | GRS 至少 99.9% (非經常性存取層為 99%)<br /><br />RA-GRS 至少 99.99% (非經常性存取層為 99.9%) | GZRS 至少 99.9% (非經常性存取層為 99%)<br /><br />RA-GZRS 至少 99.99% (非經常性存取層為 99.9%) |
| 寫入要求的可用性 SLA<sup>1</sup> | 至少 99.9% (非經常性存取層為 99%) | 至少 99.9% (非經常性存取層為 99%) | 至少 99.9% (非經常性存取層為 99%) | 至少 99.9% (非經常性存取層為 99%) |

<sup>1</sup> 如需 Azure 儲存體所保證持久性和可用性的資訊，請參閱 [Azure 儲存體 SLA](https://azure.microsoft.com/support/legal/sla/storage/)。

### <a name="durability-and-availability-by-outage-scenario"></a>持久性和可用性 (依中斷案例)

下表會指出資料在指定案例中是否為耐久及可用，其取決於儲存體帳戶中生效的備援類型：

| 中斷案例 | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| 資料中心內的節點無法使用 | 是 | 是 | 是 | 是 |
| 整個資料中心 (區域或非區域) 變成無法供使用 | 否 | 是 | 是<sup>1</sup> | 是 |
| 主要區域中發生全區域中斷 | 否 | 否 | 是<sup>1</sup> | 是<sup>1</sup> |
| 若主要區域無法使用，則次要區域的讀取存取即可提供使用 | 否 | 否 | 是 (透過 RA-GRS) | 是 (透過 RA-GZRS) |

<sup>1</sup> 若主要區域無法使用，則需要帳戶容錯移轉，才能還原寫入可用性。 如需詳細資訊，請參閱[災害復原與儲存體帳戶容錯移轉](storage-disaster-recovery-guidance.md)。

### <a name="supported-storage-account-types"></a>支援的儲存體帳戶類型

下表顯示每個儲存體帳戶類型支援的備援選項。 如需儲存體帳戶類型的資訊，請參閱[儲存體帳戶概觀](storage-account-overview.md)。

| LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|
| 一般用途 v2<br /> 一般用途 v1<br /> 區塊 blob 儲存體<br /> Blob 儲存體<br /> 檔案儲存體 | 一般用途 v2<br /> 區塊 blob 儲存體<br /> 檔案儲存體 | 一般用途 v2<br /> 一般用途 v1<br /> Blob 儲存體 | 一般用途 v2 |

所有儲存體帳戶其全部資料都會根據儲存體帳戶的備援選項進行複製。 區塊 blob、附加 blob、分頁 blob、佇列、資料表和檔案等物件都會進行複製。 所有層中的資料 (包括封存存取層) 都會進行複製。 如需 blob 層的詳細資訊，請參閱 [Azure Blob 儲存體：經常性存取層、非經常性存取層，以及封存存取層](../blobs/storage-blob-storage-tiers.md)。

如需每個備援選項的定價資訊，請參閱 [Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。

> [!NOTE]
> Azure Premium 磁碟儲存體目前僅支援本地備援儲存體 (LRS)。 區塊 blob 儲存體帳戶在某些區域支援本地備援儲存體 (LRS) 和區域備援儲存體 (ZRS)。

## <a name="data-integrity"></a>資料完整性

Azure 儲存體會使用循環冗餘檢查 (CRC) 定期驗證所儲存資料的完整性。 若偵測到資料損毀，即會使用冗餘資料進行修復。 Azure 儲存體也會計算所有網路流量的總和檢查碼，以在儲存或擷取資料時偵測資料封包的損毀。

## <a name="see-also"></a>另請參閱

- [檢查儲存體帳戶的上次同步時間屬性](last-sync-time-get.md)
- [變更儲存體帳戶的備援選項](redundancy-migration.md)
- [使用異地備援來設計高可用性的應用程式](geo-redundant-design.md)
- [災害復原和儲存體帳戶容錯移轉](storage-disaster-recovery-guidance.md)
