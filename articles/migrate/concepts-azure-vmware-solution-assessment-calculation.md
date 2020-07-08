---
title: Azure Migrate 中的 AVS 評估計算 |Microsoft Docs
description: 提供 Azure Migrate 服務中的 AVS 評估計算的總覽。
author: rashi-ms
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/25/2020
ms.author: mahain
ms.openlocfilehash: 200a6ba333d283b6a82f1eb228a0fc586b5b1fab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567211"
---
# <a name="avs-assessments-in-azure-migrate-server-assessment"></a>Azure Migrate 中的 AVS 評估：伺服器評量

[Azure Migrate](migrate-services-overview.md)提供中央中樞來追蹤內部部署應用程式和工作負載的探索、評估和遷移。 它也會將您的私用和公用雲端實例追蹤到 Azure。 中樞提供評估和遷移的 Azure Migrate 工具，以及協力廠商獨立軟體廠商（ISV）產品。

伺服器評估是 Azure Migrate 中的一項工具，可評估內部部署伺服器，以遷移至 Azure IaaS 虛擬機器和 Azure VMware 解決方案（AVS）。 本文提供如何計算 Azure VMware 解決方案（AVS）評量的相關資訊。

> [!NOTE]
> Azure VMware 解決方案（AVS）評估目前為預覽狀態，而且只能針對 VMware Vm 建立。

## <a name="types-of-assessments"></a>評量類型

使用伺服器評估所建立的評量是資料的時間點快照集。 您可以使用 Azure Migrate：伺服器評量來建立的評量有兩種。

**評量類型** | **詳細資料**
--- | --- 
**Azure VM** | 將內部部署伺服器遷移至 Azure 虛擬機器的評量。 <br/><br/> 您可以使用此評量類型，評估您的內部部署[VMware vm](how-to-set-up-appliance-vmware.md)、 [hyper-v vm](how-to-set-up-appliance-hyper-v.md)和[實體伺服器](how-to-set-up-appliance-physical.md)，以遷移至 Azure。[深入瞭解](concepts-assessment-calculation.md)
**Azure VMware 解決方案 (AVS)** | 將您的內部部署伺服器遷移至[Azure VMware 解決方案（AVS）](https://docs.microsoft.com/azure/azure-vmware/introduction)的評量。 <br/><br/> 您可以使用此評量類型，評估您的內部部署[Vmware vm](how-to-set-up-appliance-vmware.md) ，以遷移至 Azure VMware 解決方案（AVS）。[深入瞭解](concepts-azure-vmware-solution-assessment-calculation.md)

伺服器評估中的 Azure VMware 解決方案（AVS）評估提供兩個調整準則選項：

**評量** | **詳細資料** | **Data**
--- | --- | ---
**以效能為基礎** | 根據收集到的內部部署 Vm 效能資料進行評量。 | **建議的節點大小**：根據 CPU 和記憶體使用量資料，以及您為評估選取的節點類型、儲存體類型和 FTT 設定。
**作為內部部署** | 以內部部署大小調整為基礎的評估。 | **建議的節點大小**：根據內部部署 VM 大小，以及您為評估選取的節點類型、儲存體類型和 FTT 設定。

## <a name="how-do-i-run-an-assessment"></a>如何? 執行評估嗎？

有幾種方式可以執行評量。

- 使用輕量 Azure Migrate 應用裝置所收集的伺服器中繼資料來評估機器。 設備會探索內部部署機器。 然後，它會將電腦中繼資料和效能資料傳送至 Azure Migrate。
- 使用以逗號分隔值（CSV）格式匯入的伺服器中繼資料來評估機器。

## <a name="how-do-i-assess-with-the-appliance"></a>如何? 使用設備進行評估嗎？

如果您要部署 Azure Migrate 設備以探索內部部署伺服器，請執行下列步驟：

1. 設定 Azure 和您的內部部署環境，以使用伺服器評估。
2. 針對您的第一次評估，請建立 Azure 專案，並在其中新增伺服器評估工具。
3. 部署輕量 Azure Migrate 設備。 設備會持續探索內部部署機器，並將電腦中繼資料和效能資料傳送至 Azure Migrate。 將設備部署為 VM。 您不需要在要評估的電腦上安裝任何專案。

在設備開始探索機器之後，您可以將想要評估的機器收集到群組中，並使用評估類型 [ **Azure VMware 解決方案（AVS）**] 來執行群組的評量。

遵循[這裡](how-to-create-azure-vmware-solution-assessment.md)的步驟，建立您的第一個 Azure VMware 解決方案（AVS）評量。

## <a name="how-do-i-assess-with-imported-data"></a>如何? 使用匯入的資料進行評估嗎？

如果您要使用 CSV 檔案來評估伺服器，則不需要設備。 請改為執行下列步驟：

1. 設定 Azure 以使用伺服器評估。
2. 針對您的第一次評估，請建立 Azure 專案，並在其中新增伺服器評估工具。
3. 下載 CSV 範本，並在其中新增伺服器資料。
4. 將範本匯入伺服器評估。
5. 探索使用匯入新增的伺服器、將它們收集到群組中，然後針對評估類型為**Azure VMware 解決方案（AVS）** 的群組執行評量。

## <a name="what-data-does-the-appliance-collect"></a>設備會收集哪些資料？

如果您使用 Azure Migrate 設備進行評量，請瞭解針對[VMware](migrate-appliance.md#collected-data---vmware)收集的中繼資料和效能資料。

## <a name="how-does-the-appliance-calculate-performance-data"></a>設備如何計算效能資料？

如果您使用設備進行探索，它會使用下列步驟收集計算設定的效能資料：

1. 設備會收集即時取樣點。

    - **VMware vm**：每20秒會收集一次樣本點。

2. 設備會結合範例點，每隔10分鐘建立一個單一資料點。 若要建立資料點，設備會從所有範例中選取尖峰值。 然後，它會將資料點傳送至 Azure。
3. 伺服器評估會儲存上個月的所有10分鐘資料點。
4. 當您建立評估時，伺服器評估會識別要用於規模小型化優化的適當資料點。 識別是以*效能歷程記錄*和*百分位數使用率*的百分位數值為基礎。

    - 例如，如果效能歷程記錄是一周，而百分位數使用率是第95個百分位數，伺服器評估會排序上周10分鐘的樣本點。 它會依遞增順序排序，並挑選第95個百分位數的規模小型化優化值。
    - 第95個百分位數值可確保您忽略任何極端值，如果您挑選了第99個百分位數，可能會納入其中。
    - 如果您想要挑選該期間的尖峰使用量，而不想錯過任何極端值，請選取 [百分位數使用率] 的第99百分位數。

5. 這個值會乘以緩和因數，以取得設備所收集之這些計量的有效效能使用率資料：

    - CPU 使用率
    - RAM 使用率
    - 磁片 IOPS （讀取和寫入）
    - 磁片輸送量（讀取和寫入）
    - 網路輸送量（進出）

會收集下列效能資料，但不會用於針對 AVS 評量的調整大小建議：
  - 連接至 VM 的每個磁片的磁片 IOPS 和輸送量資料。
  - 針對連接至 VM 的每個網路介面卡，處理以效能為基礎之大小調整的網路 i/o。

## <a name="how-are-avs-assessments-calculated"></a>如何計算 AVS 評量？

伺服器評估會使用內部部署機器的中繼資料和效能資料來計算評量。 如果您部署 Azure Migrate 設備，評量會使用設備所收集的資料。 但是，如果您執行使用 CSV 檔案匯入的評量，則會提供用於計算的中繼資料。

這三個階段會發生計算：

1. **計算 Azure Vmware 解決方案（AVS）就緒**：內部部署 vm 是否適合遷移至 Azure vmware 解決方案（AVS）。
2. **計算每個節點的 avs 節點數目和使用率**：執行 vm 所需的估計 AVS 節點數目，以及所有節點的預估 CPU、記憶體和儲存體使用率。
3. **每月成本估計**：執行內部部署 vm 的所有 Azure VMware 解決方案（AVS）節點的預估每月成本。

計算會依照先前的順序排序。 只有當機器伺服器通過前一個階段時，電腦才會移至稍後的階段。 例如，如果伺服器無法通過 AVS 準備階段，則會標示為不適合 Azure。 未針對該伺服器進行調整大小和成本計算

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Azure VMware 解決方案（AVS）評估有哪些功能？

以下這是什麼包含在伺服器評估的 AVS 評估中：


| **屬性** | **詳細資料** 
| - | - 
| **目標位置** | 指定您要遷移到的 AVS 私人雲端位置。<br/><br/> 伺服器評估中的 AVS 評估目前支援下列目的地區域：美國東部、西歐、美國西部。 
| **儲存類型** | 指定要用於 AVS 的儲存引擎。<br/><br/> AVS 評量僅支援 vSAN 做為預設儲存體類型。 
**保留實例（RIs）** | 此屬性可協助您指定 AVS 中的保留實例。 目前不支援 AVS 節點的 RIs。 
**節點類型** | 指定用來對應內部部署 Vm 的[AVS 節點類型](https://docs.microsoft.com/azure/azure-vmware/concepts-private-clouds-clusters)。 預設節點類型為 AV36。 <br/><br/> Azure Migrate 會建議將 Vm 遷移至 AVS 所需的節點數目。 
**FTT 設定，RAID 層級** | 指定可容忍和 Raid 組合的適當失敗。 選取的 FTT 選項結合了內部部署 VM 磁片需求，將會決定 AVS 所需的 vSAN 儲存空間總計。 
**調整大小準則** | 設定要用來針對 AVS*適當調整 vm 大小*的準則。 您可以選擇以*效能為基礎*的調整大小，或*在內部部署環境中*，而不考慮效能歷程記錄。 
**效能歷程記錄** | 設定評估電腦效能資料時所要考慮的持續時間。 只有在調整大小準則是以效能為*基礎*時，此屬性才適用。 
**百分位數使用率** | 指定要針對適當調整大小所考慮之效能範例集的百分位數值。 只有在調整大小是以效能為基礎的情況下，才適用此屬性。
**緩和因數** | Azure Migrate Server 評估會考慮在評估期間的緩衝區（緩和因數）。 此緩衝區適用於 VM 的機器使用量資料 (CPU、記憶體、磁碟和網路)。 緩和因數會考量各個問題，例如季節性使用量、簡短的效能歷程記錄，以及未來可能增加的使用量。<br/><br/> 以 10 核心的 VM 為例，如果使用率為 20%，一般即相當於一個 2 核心的 VM。 但如果緩和因數為 2.0 x，則結果為 4 核心的 VM。 
**供應項目** | 顯示您所註冊的[Azure 供應](https://azure.microsoft.com/support/legal/offer-details/)專案。 Azure Migrate 會據以預估成本。
**貨幣** | 顯示您帳戶的帳單貨幣。 
**折扣 (%)** | 列出您在 Azure 供應專案上所收到的任何訂用帳戶特定折扣。 預設設定為 0%。 
**Azure Hybrid Benefit** | 指定您是否擁有軟體保證，以及是否符合[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)的資格。 雖然它不會影響以節點為基礎的 Azure VMware 解決方案價格，但客戶仍可使用 Azure 混合式權益，在 AVS 中套用內部部署的 OS 授權（Microsoft 型）。 其他軟體作業系統廠商也必須提供自己的授權條款，例如 RHEL。 
**vCPU 過度訂閱** | 指定與 AVS 節點中的一個實體核心系結之虛擬核心數目的比率。 計算中的預設值為 4 vCPU：1個在 AVS 中的實體核心。 <br/><br/> API 使用者可以將此值設定為整數。 請注意，vCPU 超額訂閱 > 4:1 可能會影響工作負載（視其 CPU 使用量而定）。 


## <a name="azure-vmware-solution-avs-suitability-analysis"></a>Azure VMware 解決方案（AVS）適用性分析

伺服器評量中的 AVS 評量會藉由檢查機器屬性，來評估每個內部部署 Vm 是否適合您的 AVS。 它也會將每個評估的電腦指派給下列其中一個適用性類別：

- **適用于 AVS**：機器可以在不進行任何變更的情況下，依現有方式遷移至 AZURE （AVS）。 它會在具有完整 AVS 支援的 AVS 中啟動。
- **備妥條件**： vm 可能會有目前 vSphere 版本的相容性問題，以及在 AVS 中可達成 vm 的完整功能之前，需要可能的 VMware 工具和或其他設定。
- **尚未準備好用於 avs**： VM 將不會在 avs 中啟動。 例如，如果內部部署 VMware VM 已連接外部裝置（例如 cd-rom），VMware VMotion 作業將會失敗（如果使用 VMware VMotion）。
- 已**就緒不明**：因為從內部部署環境收集的中繼資料不足，所以 Azure Migrate 無法判斷機器是否已就緒。

伺服器評估會檢查機器屬性，以判斷內部部署機器的 Azure 就緒程度。

### <a name="machine-properties"></a>機器屬性

伺服器評估會檢查內部部署 VM 的下列屬性，以判斷它是否可以在 Azure VMware 解決方案（AVS）上執行。


| **屬性** | **詳細資料** | **AVS 就緒狀態** 
| - | - | - 
| **網際網路通訊協定** | AVS 目前不支援 IPv6 網際網路位址。<br/><br/> 如需使用 IPv6 偵測到您的電腦，請洽詢您當地的 MSFT AVS GBB 小組以取得補救指引。| 有條件地準備好的網際網路通訊協定


### <a name="guest-operating-system"></a>客體作業系統

目前，AVS 評估不會在適用性分析中檢查作業系統。 在內部部署 Vm 上執行的所有作業系統都可能在 Azure VMware 解決方案（AVS）上執行。

除了 VM 屬性，伺服器評估也會查看機器的客體作業系統，以判斷它是否可以在 Azure 上執行。


## <a name="sizing"></a>調整大小

將電腦標示為適用于 AVS 之後，伺服器評估中的 AVS 評估會進行節點大小調整建議，其中包括識別適當的內部部署 VM 需求，以及找出所需的 AVS 節點總數。 這些建議會根據指定的評量屬性而有所不同。

- 如果評估使用以*效能為基礎*的調整大小，Azure Migrate 會考慮機器的效能歷程記錄，以提供適當的 AVS 大小調整建議。 如果您已過度配置內部部署 VM，此方法特別有用，但使用率很低，而您想要在 AVS 中適當調整 VM 的大小，以節省成本。 這個方法可協助您優化遷移期間的大小。
- 如果您不想要考慮 VM 大小調整的效能資料，而且想要將內部部署機器視為 AVS，您可以將調整大小準則設定為 [內部*部署*]。 然後，伺服器評估會根據內部部署設定來調整 Vm 大小，而不考慮使用量資料。 


### <a name="ftt-sizing-parameters"></a>FTT 調整大小參數

AVS 中使用的儲存引擎是 vSAN。 vSAN 儲存原則會定義虛擬機器的存放裝置需求。 這些原則可確保 Vm 所需的服務層級，因為它們會決定儲存體如何配置給 VM。 可用的 FTT-Raid 組合如下： 

**可容忍的失敗（FTT）** | **RAID 設定** | **最低主機需求** | **大小考慮**
--- | --- | --- | --- 
1 | RAID-1 （鏡像） | 3 | 100GB 的 VM 會使用200GB。
1 | RAID-5 （抹除編碼） | 4 | 100GB 的 VM 會耗用 133.33 GB
2 | RAID-1 （鏡像） | 5 | 100GB 的 VM 會使用 300 GB。
2 | RAID-6 （抹除編碼） | 6 | 100GB 的 VM 會使用150GB。
3 | RAID-1 （鏡像） | 7 | 100GB 的 VM 會使用 400 GB。

### <a name="performance-based-sizing"></a>以效能為基礎調整大小

針對以效能為基礎的大小調整，伺服器評估會從連接至 VM 的磁片開始，接著是網路介面卡。 然後，它會將 VM 需求對應到適用于 AVS 的適當無節點。 Azure Migrate 設備會分析內部部署環境，以收集 CPU、記憶體、磁片和網路介面卡的效能資料。

**效能資料收集步驟：**

1. 針對 VMware Vm，Azure Migrate 設備會收集每20秒間隔的即時取樣點。 
2. 設備會匯總每10分鐘收集的樣本點，並將過去10分鐘的最大值傳送至伺服器評估。
3. 伺服器評估會儲存過去一個月的所有10分鐘範例點。 然後，根據針對 [*效能*歷程] 和 [*百分位數使用率*] 指定的評量屬性，識別要用於適當調整大小的適當資料點。 例如，如果效能歷程記錄設定為1天，而百分位數使用率是第95個百分位數，則伺服器評估會在最後一天使用10分鐘取樣點，以遞增順序排序，並挑選第95個百分位數值來進行右調整大小。
4. 這個值會乘以緩和因數，以取得每個計量（CPU 使用率、記憶體使用率、磁片 IOPS （讀取和寫入）、磁片輸送量（讀取和寫入），以及設備收集的網路輸送量（輸入和輸出）的有效效能使用率資料。

判斷有效使用率值之後，儲存體、網路和計算大小的處理方式如下所示。

**儲存體大小**： Azure Migrate 使用總內部部署 VM 磁碟空間作為計算參數，以判斷除了客戶選取的 FTT 設定以外的 AVS vSAN 儲存需求。 FTT-可容忍的失敗，以及每個 FTT 選項只需要最少節點，會決定所需的總 vSAN 儲存體與 VM 磁片需求結合。

**網路大小**：伺服器評估目前不會針對 AVS 評量考慮任何網路設定。

**計算大小**：計算儲存體需求之後，伺服器評估會考慮 CPU 和記憶體需求，以根據節點類型判斷 AVS 所需的節點數目。

- 根據調整大小準則，伺服器評估會查看以效能為基礎的 VM 資料或內部部署 VM 設定。 緩和因數設定允許指定叢集的成長因數。 目前預設會啟用超執行緒，因此36核心節點將會有72虛擬核心。 4虛擬核心每個實體用來判斷每個叢集的 CPU 閾值，使用不超過80% 使用率的 VMware 標準，以允許處理維護或失敗，而不會危及叢集可用性。 目前沒有覆寫可用來變更超額訂閱值，我們在未來的版本中可能會有此情況。

### <a name="as-on-premises-sizing"></a>作為內部部署調整大小

如果您使用*做為內部部署*調整大小，伺服器評估不會考慮 vm 和磁片的效能歷程記錄。 相反地，它會根據內部部署所配置的大小來配置 AVS 節點。 預設儲存體類型是 AVS 中的 vSAN。

## <a name="confidence-ratings"></a>信賴評等

Azure Migrate 中的每個以效能為基礎的評量，都與信賴評等相關聯，範圍從一（最低）到五顆星（最高）。

- 根據計算評量所需的資料點可用性，每個評量都會指派信賴評等。
- 評量的信賴評等可協助您評估 Azure Migrate 提供的大小建議之可靠性。
- 信賴評等不適用於*作為內部部署*評量。
- 針對以效能為基礎的大小調整，伺服器評估中的 AVS 評量需要 CPU 和 VM 記憶體的使用量資料。 會收集下列資料，但不會用於 AVS 的調整大小建議：
  - 連接至 VM 的每個磁片的磁片 IOPS 和輸送量資料。
  - 針對連接至 VM 的每個網路介面卡，處理以效能為基礎之大小調整的網路 i/o。

  如果 vCenter Server 中有任何這些使用量號碼無法使用，則大小建議可能不可靠。

根據可用資料點的百分比，評量的信賴評等如下所示。


| **資料點的可用性** | **信賴評等** |
| - | - |
| 0-20% | 1 顆星 |
| 21-40% | 2 顆星 |
| 41-60% | 3 顆星 |
| 61-80% | 4 顆星 |
| 81-100% | 5 顆星 |

### <a name="low-confidence-ratings"></a>低信賴等級

以下是評估可能獲得低信賴等級的幾個原因：

- 您在建立評量的持續時間內，未針對您的環境進行分析。 例如，如果您建立的評量將效能持續時間設定為一天，則在開始探索所有資料點之後，您必須至少等候一天，以進行收集。
- 部分 VM 在評量計算期間關閉。 如果有任何 Vm 在某段期間內關閉，伺服器評估就無法收集該期間的效能資料。
- 有些 Vm 是在計算評估期間建立的。 例如，如果您為上個月的效能歷程記錄建立了評量，但有些 Vm 只是在一周前于環境中建立，則新 Vm 的效能歷程記錄將不會存在於完整的持續時間內。

> [!NOTE]
> 如果任何評量的信賴評等少於五顆星，建議您至少等候一天，讓設備分析環境，然後重新計算評量。 如果您不這麼做，以效能為基礎的大小調整可能不可靠。 在此情況下，建議您將評量切換為內部部署調整大小。

## <a name="monthly-cost-estimation"></a>每月成本預估

調整大小建議完成後，Azure Migrate 會將節點價格所需的 AVS 節點數目相乘，以計算在 AVS 中執行內部部署工作負載的總成本。 每個 VM 成本的成本計算方式是將總成本除以評量中的 Vm 數目。 
- 計算會將所需的節點數目、節點類型和位置納入考慮。
- 它會匯總所有節點的成本，以計算每月總成本。
- 成本會以評量設定中指定的貨幣顯示。

因為 Azure VMware 解決方案（AVS）的定價是每個節點，所以總成本沒有計算成本和儲存體成本分佈。 [深入了解](https://docs.microsoft.com/azure/azure-vmware/introduction)

請注意，當 Azure VMware 解決方案（AVS）處於預覽狀態時，評量中的節點價格是預覽價格。 如需指引，請洽詢您當地的 MSFT AVS GBB 小組。

## <a name="migration-tool-guidance"></a>遷移工具指引

在 Azure VMware 解決方案（AVS）評估的 Azure 就緒狀態報表中，您可以看到下列建議的工具： 
- **VMWARE HCX 或 Enterprise**：針對 vmware 機器，vmware 混合式雲端擴充功能（HCX）解決方案是建議的遷移工具，可將內部部署工作負載遷移至 Azure VMware 解決方案（AVS）私人雲端。 [深入了解](https://docs.microsoft.com/azure/azure-vmware/hybrid-cloud-extension-installation)。
- **未知**：對於透過 CSV 檔案匯入的機器，預設的遷移工具是未知的。 但對於 VMware 機器，建議使用 VMWare 混合式雲端擴充功能（HCX）解決方案。

## <a name="next-steps"></a>後續步驟

建立適用于[AVS VMware vm](how-to-create-azure-vmware-solution-assessment.md)的評量。
