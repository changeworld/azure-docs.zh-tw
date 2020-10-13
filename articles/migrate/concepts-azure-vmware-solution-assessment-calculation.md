---
title: Azure Migrate 中的 AVS 評定計算 |Microsoft Docs
description: 提供 Azure Migrate 服務中 AVS 評量計算的總覽。
author: rashi-ms
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/25/2020
ms.author: mahain
ms.openlocfilehash: 400c2d91383b5f21fcd40fdbbe279bd83fcef51a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91576535"
---
# <a name="server-assessment-overview-migrate-to-azure-vmware-solution"></a>伺服器評量總覽 (遷移至 Azure VMware 解決方案) 

[Azure Migrate](migrate-services-overview.md) 提供中央中樞，可追蹤內部部署應用程式和工作負載的探索、評估和遷移。 它也會追蹤您的私人和公用雲端實例至 Azure。 中樞提供 Azure Migrate 的評定和遷移工具，以及協力廠商獨立軟體廠商 (ISV) 產品。

伺服器評量是 Azure Migrate 中的一項工具，可評估內部部署伺服器，以遷移至 Azure IaaS 虛擬機器和 Azure VMware Solution (AVS) 。 本文提供如何計算 Azure VMware Solution (AVS) 評量的相關資訊。

> [!NOTE]
> Azure VMware 解決方案 (AVS) 評量目前為預覽狀態，而且只能針對 VMware Vm 建立。

## <a name="types-of-assessments"></a>評量類型

使用伺服器評量建立的評量是資料的時間點快照集。 您可以使用 Azure Migrate：伺服器評量來建立的評量有兩種。

**評量類型** | **詳細資料**
--- | --- 
**Azure VM** | 評估內部部署伺服器，並將其遷移至 Azure 虛擬機器。 <br/><br/> 您可以使用此評量類型來評定您的內部部署 [VMware vm](how-to-set-up-appliance-vmware.md)、 [hyper-v vm](how-to-set-up-appliance-hyper-v.md)和 [實體伺服器](how-to-set-up-appliance-physical.md) ，以遷移至 Azure。[深入瞭解](concepts-assessment-calculation.md)
**Azure VMware 解決方案 (AVS)** | 評估內部部署伺服器，並將其遷移至 [Azure VMware 解決方案 (AVS)](../azure-vmware/introduction.md)。 <br/><br/> 您可以使用此評量類型，評量內部部署 [VMware VM](how-to-set-up-appliance-vmware.md) 以移轉至 Azure VMware 解決方案 (AVS)。[深入了解](concepts-azure-vmware-solution-assessment-calculation.md)

在伺服器評量中 (AVS) 評量的 Azure VMware 解決方案提供兩個調整大小準則選項：

**評量** | **詳細資料** | **Data**
--- | --- | ---
**以效能為基礎** | 以內部部署 Vm 收集的效能資料為基礎的評量。 | **建議的節點大小**：根據 CPU 和記憶體使用率資料，以及您針對評量選取的節點類型、儲存體類型和 FTT 設定。
**作為內部部署** | 以內部部署大小調整為基礎的評估。 | **建議的節點大小**：根據內部部署 VM 大小，以及您為評量選取的節點類型、儲存體類型和 FTT 設定。

## <a name="how-do-i-run-an-assessment"></a>如何? 執行評量？

有幾種方式可以執行評量。

- 使用輕量 Azure Migrate 設備所收集的伺服器中繼資料來評估機器。 設備會探索內部部署機器。 然後，它會將電腦中繼資料和效能資料傳送至 Azure Migrate。
- 使用以逗號分隔值匯入的伺服器中繼資料來評定機器 (CSV) 格式。

## <a name="how-do-i-assess-with-the-appliance"></a>如何? 使用設備進行評估嗎？

如果您要部署 Azure Migrate 設備以探索內部部署伺服器，請執行下列步驟：

1. 設定 Azure 和您的內部部署環境，以使用伺服器評量。
2. 針對您的第一次評量，請建立 Azure 專案，並在其中新增伺服器評量工具。
3. 部署輕量 Azure Migrate 設備。 設備會持續探索內部部署電腦，並將電腦中繼資料和效能資料傳送至 Azure Migrate。 將設備部署為 VM。 您不需要在您想要評估的機器上安裝任何資訊。

在設備開始探索機器之後，您可以將您想要評估的機器收集到群組中，並針對具有評量類型 [ **Azure VMware 解決方案 (AVS) **的群組執行評量。

遵循 [此處](how-to-create-azure-vmware-solution-assessment.md)的步驟，建立您的第一個 Azure VMware 解決方案 (AVS) 評量。

## <a name="how-do-i-assess-with-imported-data"></a>如何? 使用匯入的資料來評估嗎？

如果您要使用 CSV 檔案評估伺服器，就不需要設備。 請改為執行下列步驟：

1. 設定 Azure 以使用伺服器評量。
2. 針對您的第一次評量，請建立 Azure 專案，並在其中新增伺服器評量工具。
3. 下載 CSV 範本，並將伺服器資料新增至其中。
4. 將範本匯入到伺服器評量。
5. 探索使用匯入新增的伺服器、將它們收集到群組中，然後針對具有評量類型 [ **Azure VMware Solution (AVS) **的群組執行評量。

## <a name="what-data-does-the-appliance-collect"></a>設備會收集哪些資料？

如果您是使用 Azure Migrate 設備進行評量，請瞭解針對 [VMware](migrate-appliance.md#collected-data---vmware)收集的中繼資料和效能資料。

## <a name="how-does-the-appliance-calculate-performance-data"></a>設備如何計算效能資料？

如果您使用設備進行探索，則會使用下列步驟收集計算設定的效能資料：

1. 設備會收集即時範例點。

    - **VMware vm**：每隔20秒會收集一個範例點。

2. 設備會結合範例點，每隔10分鐘建立一個資料點。 若要建立資料點，設備會選取所有樣本的尖峰值。 然後，它會將資料點傳送至 Azure。
3. 伺服器評量會儲存上個月的10分鐘資料點。
4. 當您建立評量時，伺服器評量會識別適合用於擔心的資料點。 識別是以 *效能歷程記錄* 和 *百分位數使用率*的百分位數值為基礎。

    - 例如，如果效能歷程記錄為一周，而百分位數使用率為第95個百分位數，伺服器評量會將上周的10分鐘樣本點排序。 它會以遞增順序排序，並挑選第95個百分位數值來進行擔心。
    - 第95個百分位數值可確保您會忽略任何極端值，如果您挑選第99個百分位數，這些極端值可能會包含在內。
    - 如果您想要挑選期間的尖峰使用量，但不想遺漏任何極端值，請選取百分位數使用率的第99百分位數。

5. 此值會乘以緩和因數，以取得設備所收集之這些計量的有效效能使用率資料：

    - CPU 使用率
    - RAM 使用率
    - 磁片 IOPS (讀取和寫入) 
    - 磁片輸送量 (讀取和寫入) 
    -  (in 和 out) 的網路輸送量

下列效能資料會收集，但不會用於 AVS 評量的大小調整建議：
  - 連接至 VM 的每個磁片的磁片 IOPS 和輸送量資料。
  - 針對連接至 VM 的每個網路介面卡，處理以效能為基礎的大小調整的網路 i/o。

## <a name="how-are-avs-assessments-calculated"></a>如何計算 AVS 評定？

伺服器評量會使用內部部署電腦的中繼資料和效能資料來計算評量。 如果您部署 Azure Migrate 設備，評量會使用設備所收集的資料。 但是，如果您執行使用 CSV 檔案匯入的評量，則會提供計算的中繼資料。

計算會在下列三個階段中發生：

1. **計算 Azure VMware 解決方案 (AVS) 準備**：內部部署 vm 是否適合遷移至 Azure VMware SOLUTION (AVS) 。
2. **計算 avs 節點數目和跨節點的使用率**：在所有節點上執行 vm 所需的 avs 節點估計數目，以及預計的 CPU、記憶體和儲存體使用量。
3. **每月成本估計**：所有 Azure VMware 解決方案的每月預估成本 (AVS) 執行內部部署 vm 的節點。

計算順序為先前的順序。 電腦伺服器只會在通過上一個階段時移至較新的階段。 例如，如果伺服器無法通過 AVS 就緒階段，它會標示為不適合 Azure。 未針對該伺服器進行調整大小和成本計算

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Azure VMware 解決方案有哪些 (AVS) 評量？

以下是在伺服器評量中包含于 AVS 評量的功能：


| **屬性** | **詳細資料** 
| - | - 
| **目標位置** | 指定您想要遷移的 AVS 私用雲端位置。<br/><br/> 伺服器評量中的 AVS 評量目前支援下列目的地區域：美國東部、西歐和美國西部。 
| **儲存體類型** | 指定要用於 AVS 的儲存引擎。<br/><br/> AVS 評量僅支援 vSAN 作為預設儲存體類型。 
** (RIs) 的保留實例 ** | 這個屬性可協助您指定 AVS 中的保留實例。 目前不支援 AVS 節點的 RIs。 
**節點類型** | 指定用來對應內部部署 Vm 的 [AVS 節點類型](../azure-vmware/concepts-private-clouds-clusters.md) 。 預設節點類型為 AV36。 <br/><br/> Azure Migrate 會建議將 Vm 遷移至 AVS 所需的節點數目。 
**FTT 設定，RAID 層級** | 指定可容忍和 Raid 組合的適用失敗。 選取的 FTT 選項與內部部署 VM 磁片需求結合，將會決定 AVS 中所需的 vSAN 儲存體總數。 
**調整大小準則** | 設定要用來為 AVS *適當調整 vm 大小* 的準則。 您可以選擇以 *效能為基礎* 的大小調整或 *內部部署* ，而不考慮效能歷程記錄。 
**效能歷程記錄** | 設定評估電腦效能資料時要考慮的持續時間。 只有當調整大小準則是以效能為 *基礎*時，才適用這個屬性。 
**百分位數使用率** | 指定要考慮用於適當調整大小之效能樣本集的百分位數值。 只有在調整大小為以效能為基礎時，才適用這個屬性。
**緩和因數** | Azure Migrate 伺服器評量會考慮在評量期間 (緩和因素) 的緩衝區。 此緩衝區適用於 VM 的機器使用量資料 (CPU、記憶體、磁碟和網路)。 緩和因數會考量各個問題，例如季節性使用量、簡短的效能歷程記錄，以及未來可能增加的使用量。<br/><br/> 以 10 核心的 VM 為例，如果使用率為 20%，一般即相當於一個 2 核心的 VM。 但如果緩和因數為 2.0 x，則結果為 4 核心的 VM。 
**供應項目** | 顯示您所註冊的 [Azure 供應](https://azure.microsoft.com/support/legal/offer-details/) 專案。 Azure Migrate 會據以預估成本。
**貨幣** | 顯示帳戶的帳單貨幣。 
**折扣 (%)** | 列出您在 Azure 供應專案上收到的任何訂用帳戶特定折扣。 預設設定為 0%。 
**Azure Hybrid Benefit** | 指定您是否擁有軟體保證，並符合 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)資格。 雖然它對 Azure VMware 解決方案的定價沒有任何影響，因為以節點為基礎的價格，客戶仍然可以使用 Azure 的混合式權益，將內部部署的 OS 授權套用到 AVS (Microsoft) 。 其他軟體 OS 廠商將必須提供自己的授權條款，例如 RHEL。 
**vCPU 超額訂閱** | 指定系結至 AVS 節點中一個實體核心的虛擬核心數目比率。 計算中的預設值為 4 vCPU： AVS 中的1個實體核心。 <br/><br/> API 使用者可以將此值設定為整數。 請注意，vCPU 超額 > 4:1 可能會影響工作負載，視其 CPU 使用量而定。 


## <a name="azure-vmware-solution-avs-suitability-analysis"></a>Azure VMware 解決方案 (AVS) 適用性分析

伺服器評量中的 AVS 評量會藉由檢查電腦屬性來評估每個內部部署 Vm 是否適合 AVS。 它也會將每個評估的機器指派給下列其中一個適用性類別：

- **適用于 avs**：機器可以依原樣遷移至 AZURE (AVS) ，而不需要任何變更。 它會在具有完整 AVS 支援的 AVS 中開始。
- **備妥條件**： vm 可能會有目前 vSphere 版本的相容性問題，而且需要有可能的 VMware 工具和或其他設定，才能在 AVS 中達成完整的虛擬機器功能。
- **尚未準備好用於 avs**： VM 將不會在 avs 中啟動。 例如，如果內部部署 VMware VM 有連接的外部裝置（例如 cd-rom），則如果使用 VMware VMotion) ，VMware VMotion 操作將會失敗 (。
- **就緒狀態不明**： Azure Migrate 因為從內部部署環境收集的中繼資料不足，所以無法判斷電腦是否就緒。

伺服器評量會審核機器屬性，以判斷內部部署機器的 Azure 就緒程度。

### <a name="machine-properties"></a>機器屬性

伺服器評量會檢查內部部署 VM 的下列屬性，以判斷它是否可以在 Azure VMware Solution (AVS) 上執行。


| **屬性** | **詳細資料** | **AVS 就緒狀態** 
| - | - | - 
| **網際網路通訊協定** | AVS 目前不支援 IPv6 網際網路定址。<br/><br/> 若系統偵測到您的電腦使用 IPv6，請連絡您當地的 MSFT AVS GBB 小組以取得補救指引。| 有條件地準備好網際網路通訊協定


### <a name="guest-operating-system"></a>客體作業系統

目前，AVS 評量不會在適用性分析中檢查作業系統。 在內部部署 Vm 上執行的所有作業系統都可能在 Azure VMware Solution (AVS) 上執行。

除了 VM 屬性，伺服器評量還會查看機器的客體作業系統，以判斷它是否可以在 Azure 上執行。


## <a name="sizing"></a>調整大小

電腦標示為可供 AVS 使用之後，伺服器評量中的 AVS 評量會進行節點大小調整建議，其牽涉到識別適當的內部部署 VM 需求，以及尋找所需的 AVS 節點總數。 這些建議會根據指定的評量屬性而有所不同。

- 如果評量使用以 *效能為基礎的大小調整*，Azure Migrate 會考慮機器的效能歷程記錄，以針對 AVS 做出適當的大小調整建議。 如果您已過度配置內部部署 VM，則此方法特別有用，但使用率很低，而您想要將 AVS 中的 VM 調整為適當的大小，以節省成本。 此方法可協助您在遷移期間優化大小。
- 如果您不想要考慮 VM 大小調整的效能資料，而且想要將內部部署電腦依原樣帶到 AVS，則可以將調整大小準則設定為 *內部部署*。 然後，伺服器評量會根據內部部署設定來調整 Vm 大小，而不考慮使用量資料。 


### <a name="ftt-sizing-parameters"></a>FTT 大小調整參數

AVS 中使用的儲存引擎是 vSAN。 vSAN 儲存體原則會定義虛擬機器的儲存體需求。 這些原則可確保 VM 所需的服務層級，因為原則會決定如何將儲存體配置給 VM。 可用的 FTT-Raid 組合如下： 

**可容許的失敗 (FTT)** | **RAID 組態** | **最低主機需求** | **大小考量**
--- | --- | --- | --- 
1 | RAID-1 (鏡像) | 3 | 100GB 的 VM 會耗用 200GB。
1 | RAID-5 (抹除編碼) | 4 | 100GB 的 VM 會耗用 133.33GB
2 | RAID-1 (鏡像) | 5 | 100GB 的 VM 會耗用 300GB。
2 | RAID-6 (抹除編碼) | 6 | 100GB 的 VM 會耗用 150GB。
3 | RAID-1 (鏡像) | 7 | 100GB 的 VM 會耗用 400GB。

### <a name="performance-based-sizing"></a>以效能為基礎調整大小

針對以效能為基礎的大小調整，伺服器評量會從連接至 VM 的磁片開始，然後是網路介面卡。 然後，它會將 VM 需求對應至適用于 AVS 的適當節點。 Azure Migrate 設備會分析內部部署環境，以收集 CPU、記憶體、磁片和網路介面卡的效能資料。

**效能資料收集步驟：**

1. 針對 VMware Vm，Azure Migrate 設備會以每隔20秒的間隔收集即時範例點。 
2. 設備會匯總每隔10分鐘收集的樣本點，並將過去10分鐘的最大值傳送到伺服器評量。
3. 伺服器評量會儲存最後一個月的10分鐘範例點。 然後，根據針對 *效能歷程記錄* 和 *百分位數使用率*所指定的評量屬性，它會識別適當的資料點以用於正確調整大小。 例如，如果效能歷程記錄設定為1天，而百分位數使用率為第95個百分位數，伺服器評量會使用過去一天的10分鐘取樣點，以遞增順序排序，並挑選第95個百分位數的值來進行正確調整大小。
4. 此值會乘以緩和因數，以取得每個度量的有效效能使用率資料 (CPU 使用率、記憶體使用率、磁片 IOPS (讀取和寫入) 、磁片輸送量 (讀取和寫入) ，以及 (設備所收集) 的網路輸送量。

判斷有效的使用率值之後，儲存體、網路和計算大小調整的處理方式如下所示。

**儲存體大小調整**： Azure Migrate 使用內部部署 VM 磁碟空間總計作為計算參數，除了客戶選取的 FTT 設定以外，還會判斷 AVS vSAN 儲存體需求。 FTT-可容忍的失敗，以及每個 FTT 選項都不需要節點數下限，將會決定所需的總 vSAN 儲存體與 VM 磁片需求的組合。

**調整網路大小**：伺服器評量目前不會考慮將任何網路設定納入 AVS 評量。

**計算大小調整**：在計算存放裝置需求之後，伺服器評量會考慮 CPU 和記憶體需求，以根據節點類型判斷 AVS 所需的節點數目。

- 根據調整規模準則，伺服器評量會查看以效能為基礎的 VM 資料或內部部署 VM 設定。 緩和因素設定可讓您指定叢集的成長因數。 目前預設會啟用超執行緒，因此 36 個核心節點將會有 72 個虛擬核心。 每個實體會以不超過 80% 使用率的 VMware 標準，使用 4 個虛擬核心來判斷每個叢集的 CPU 閾值，以便在不會危及叢集可用性的情況下，允許處理維護或失敗。 目前沒有可用來變更超額訂閱值的覆寫，在未來的版本中可能會有這種情況。

### <a name="as-on-premises-sizing"></a>作為內部部署調整大小

如果您使用 *作為內部部署*調整大小，伺服器評量不會考慮 vm 和磁片的效能歷程記錄。 相反地，它會根據內部部署所配置的大小來配置 AVS 節點。 預設儲存體類型為 AVS 中的 vSAN。

## <a name="confidence-ratings"></a>信賴評等

Azure Migrate 中每個以效能為基礎的評量與信賴評等相關聯，其範圍從一 (最低) 到五顆星 (最高) 。

- 根據計算評量所需的資料點可用性，每個評量都會指派信賴評等。
- 評量的信賴評等可協助您評估 Azure Migrate 提供的大小建議之可靠性。
- 信賴評等不適用於 *作為內部部署* 評量。
- 針對以效能為基礎的大小調整，伺服器評量中的 AVS 評定需要 CPU 和 VM 記憶體的使用量資料。 下列資料已收集，但不適用於 AVS 的大小調整建議：
  - 連接至 VM 的每個磁片的磁片 IOPS 和輸送量資料。
  - 針對連接至 VM 的每個網路介面卡，處理以效能為基礎的大小調整的網路 i/o。

  如果 vCenter Server 中有任何這些使用量數位無法使用，則大小建議可能不可靠。

根據可用資料點的百分比，評量的信賴評等如下所示。


| **資料點的可用性** | **信賴評等** |
| - | - |
| 0-20% | 1 顆星 |
| 21-40% | 2 顆星 |
| 41-60% | 3 顆星 |
| 61-80% | 4 顆星 |
| 81-100% | 5 顆星 |

### <a name="low-confidence-ratings"></a>低信賴等級

以下是評估可能獲得低信賴評等的一些原因：

- 您未在建立評量的期間對環境進行分析。 例如，如果您建立的評量將效能持續時間設定為一天，則您必須在開始探索之後至少等候一天，才能收集所有資料點。
- 部分 VM 在評量計算期間關閉。 如果有一段時間關閉任何 Vm，伺服器評量就無法收集該期間的效能資料。
- 某些 Vm 是在計算評估的期間內建立的。 例如，如果您針對上個月的效能歷程建立了評量，但有些 Vm 只是在一周前的環境中建立，則新 Vm 的效能歷程記錄將不會存在於完整的期間內。

> [!NOTE]
> 如果任何評量的信賴評等少於五顆星，建議您至少等候一天，讓設備分析環境，然後重新計算評量。 如果不是，以效能為基礎的大小調整可能不可靠。 在此情況下，我們建議您將評量切換至內部部署調整大小。

## <a name="monthly-cost-estimation"></a>每月成本預估

大小調整建議完成之後，Azure Migrate 會將節點價格所需的 AVS 節點數目相乘，以計算在 AVS 中執行內部部署工作負載的總成本。 每個 VM 成本的計算方式是將總成本除以評量中的 Vm 數目。 
- 計算會將所需的節點數目、節點類型和位置列入考慮。
- 它會匯總所有節點之間的成本，以計算每月總成本。
- 成本會以評量設定中指定的貨幣顯示。

因為 Azure VMware 解決方案 (AVS) 的定價是每個節點，所以總成本沒有計算成本和儲存體成本分配。 [深入了解](../azure-vmware/introduction.md)

請注意，由於 Azure VMware Solution (AVS) 處於預覽狀態，因此評量中的節點價格為預覽價格。 如需指引，請洽詢您當地的 MSFT AVS GBB 團隊。

## <a name="migration-tool-guidance"></a>遷移工具指導方針

在 Azure VMware 解決方案 (AVS) 評量的 Azure 移轉整備程度報表中，您可以看到下列建議的工具： 
- **VMWARE HCX 或企業**：若為 vmware 機器，vmware 混合式雲端擴充 (HCX) 解決方案是建議的遷移工具，可將內部部署工作負載遷移至 Azure VMware 解決方案 (AVS) 私用雲端。 [深入了解](../azure-vmware/tutorial-deploy-vmware-hcx.md)。
- **未知**：針對透過 CSV 檔案匯入的電腦，預設的移轉工具是未知的。 針對 VMware 機器，建議使用 VMware 混合式雲端擴充功能 (HCX) 解決方案。

## <a name="next-steps"></a>後續步驟

建立適用于 [AVS VMware vm](how-to-create-azure-vmware-solution-assessment.md)的評量。
