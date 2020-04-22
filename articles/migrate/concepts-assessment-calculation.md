---
title: Azure 移植伺服器評估中的評估
description: 瞭解 Azure 移植伺服器評估中的評估
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 2f76ea5f195be2914cdcdb4de9e93af38504d66e
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769929"
---
# <a name="assessments-in-azure-migrate-server-assessment"></a>Azure 移轉中的評估:伺服器評估

本文概述了[Azure 遷移:伺服器評估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具中的評估。 該工具可以評估本地 VMware 虛擬機器、超 VM 和實體伺服器,以便遷移到 Azure。

## <a name="whats-an-assessment"></a>什麼是評估?

使用「伺服器評估」工具進行評估可測量就緒情況,並估計將本地伺服器遷移到 Azure 的效果。

> [!NOTE]
> 在 Azure 政府中,查看[支持的目標](migrate-support-matrix.md#supported-geographies-azure-government)評估位置。 請注意,評估中的 VM 大小建議將專門用於政府雲區域的 VM 系列。 [瞭解有關](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines)VM 類型的資訊。

## <a name="types-of-assessments"></a>評估類型

使用伺服器評估創建的評估是數據的時間點快照。 伺服器評估提供兩種類型的評估。

**評量類型** | **詳細資料** | **Data**
--- | --- | ---
**以效能為基礎** | 根據收集的績效資料提出建議的評估 | VM 大小建議基於 CPU 和 RAM 利用率數據。<br/><br/> 磁碟類型建議基於每秒輸入/輸出操作 (IOPS) 和本地磁碟的輸送量。 磁碟類型包括 Azure 標準硬碟、Azure 標準 SSD 和 Azure 高級磁碟。
**本地端** | 不使用效能資料提出建議的評估 | VM 大小建議基於本地 VM 大小。<br/><br> 建議的磁碟類型基於評估的選定存儲類型。

## <a name="how-do-i-run-an-assessment"></a>如何運行評估?

有幾種方法可以運行評估。

- 使用輕量級 Azure 遷移設備收集的伺服器元數據評估計算機。 設備會探索內部部署機器。 然後,它將計算機中數據和性能數據發送到 Azure 遷移。
- 使用以逗號分隔值 (CSV) 格式導入的伺服器元數據評估電腦。

## <a name="how-do-i-assess-with-the-appliance"></a>如何使用產品進行評估?

如果要部署 Azure 移轉裝置以發現本地伺服器,請執行以下步驟:

1. 設置 Azure 和本地環境以使用伺服器評估。
1. 對於第一次評估,請創建 Azure 專案,並將伺服器評估工具添加到其中。
1. 部署輕量級 Azure 遷移設備。 設備不斷發現本地電腦,並將計算機中數據和性能數據發送到 Azure 遷移。 將設備部署為 VM 或物理電腦。 您無需在要評估的電腦上安裝任何內容。

設備開始發現機器后,您可以將要評估的計算機收集到組中,並為組運行評估。

請按照[VMware、Hyper-V](tutorial-prepare-vmware.md)或[物理伺服器](tutorial-prepare-physical.md)的教程來試用[Hyper-V](tutorial-prepare-hyper-v.md)這些步驟。

## <a name="how-do-i-assess-with-imported-data"></a>如何使用導入的數據進行評估?

如果使用 CSV 檔評估伺服器,則不需要設備。 相反,執行以下步驟:

1. 將 Azure 設置為使用伺服器評估。
1. 對於第一次評估,請創建 Azure 專案,並將伺服器評估工具添加到其中。
1. 下載 CSV 範本並將其添加到其中。
1. 將範本導入伺服器評估。
1. 發現隨導入一起添加的伺服器,將它們收集到組中,併為組運行評估。

## <a name="what-data-does-the-appliance-collect"></a>產品收集哪些數據?

如果使用 Azure 遷移設備進行評估,請瞭解為[VMware](migrate-appliance.md#collected-data---vmware)和[Hyper-V](migrate-appliance.md#collected-data---hyper-v)收集的中繼資料和效能數據。

## <a name="how-does-the-appliance-calculate-performance-data"></a>設備如何計算性能數據?

如果使用裝置進行發現,它將通過以下步驟收集計算設置的性能數據:

1. 產品收集即時採樣點。

    - **VMware VM**:每 20 秒收集一個採樣點。
    - **超 VM**:每 30 秒收集一個採樣點。
    - **物理伺服器**:每五分鐘收集一個採樣點。

1. 設備將採樣點合併,每 10 分鐘創建一個數據點。 要創建數據點,設備將從所有範例中選擇峰值。 然後,它將數據點發送到 Azure。
1. 伺服器評估存儲上個月的所有 10 分鐘數據點。
1. 創建評估時,伺服器評估將標識用於許可權化的相應數據點。 標識基於*性能歷史記錄*和*百分位數利用率*的百分位值。

    - 例如,如果性能歷史記錄為一周,百分位數利用率為第 95 百分位,則伺服器評估會對上周的 10 分鐘採樣點進行排序。 它按升序排序,並選擇第 95 個百分位值進行權利化。
    - 第 95 個百分位值可確保忽略任何異常值,如果選取第 99 個百分位,可能會包括這些異常值。
    - 如果要選擇期間的峰值使用率,並且不想錯過任何異常值,請選擇第 99 百分位數,瞭解百分位數利用率。

1. 此值乘以舒適係數,以獲得設備收集的這些指標的有效性能利用率數據:

    - CPU 使用率
    - RAM 利用率
    - 磁碟 IOPS(讀寫)
    - 磁碟輸送量(讀取和寫入)
    - 網路輸送量(進出)

## <a name="how-are-assessments-calculated"></a>如何計算評估?

伺服器評估使用本地電腦的中繼資料和性能數據來計算評估。 如果部署 Azure 遷移設備,評估將使用設備收集的數據。 但是,如果運行使用 CSV 檔導入的評估,則提供計算的元數據。

計算發生在以下三個階段:

1. **計算 Azure 就緒性**:評估電腦是否適合遷移到 Azure。
1. **計算大小調整建議**:估計計算、存儲和網路大小調整。
1. **計算每月成本**:計算遷移后在 Azure 中運行計算機的估計每月計算和儲存成本。

計算按上述順序排列。 僅當計算機伺服器通過前一階段時,計算機伺服器才會移動到稍後階段。 例如,如果伺服器在 Azure 就緒階段失敗,則將其標記為不適合 Azure。 未為該伺服器執行大小調整和成本計算。

## <a name="whats-in-an-assessment"></a>評估包含什麼？

以下是伺服器評估中評估中包含的內容:

屬性 | 詳細資料
--- | ---
**目標位置** | 要遷移到的位置。 伺服器評估目前支援這些目標 Azure 區域:<br/><br/> 澳大利亞東部、澳大利亞東南部、巴西南部、加拿大中部、加拿大東部、印度中部、美國中部、中國東部、中國北部、東亞、美國東部、美國東部 2、德國中部、德國東北部、日本東部、日本西部、韓國中部、韓國中部、美國中北部、北歐、美國中南部、東南亞、南印度、英國南部、英國西部、美國亞利桑那州州長、美國德克薩斯州州長、美國弗吉尼亞州州長,美國中西部、西歐、西印度、美國西部和美國西部 2.
**目標儲存磁碟(大小調整)** | 用於在 Azure 中儲存的磁碟類型。 <br/><br/> 將目標儲存磁碟指定為進階管理、標準 SSD 管理或標準硬碟管理。
**目標儲存磁碟(基於效能的大小調整)** | 指定目標儲存磁碟的類型為自動、進階管理、標準硬碟管理或標準 SSD 管理。<br/><br/> **自動**:磁碟建議基於磁碟的性能數據,即IOPS和輸送量。<br/><br/>**進階或標準**:評估建議在所選儲存類型內使用磁碟 SKU。<br/><br/> 如果希望單實例 VM 服務級別協定 (SLA) 為 99.9%,請考慮使用高級託管磁碟。 此使用可確保建議將評估中的所有磁碟作為高級管理磁碟。<br/><br/> Azure 遷移僅支援用於遷移評估的託管磁碟。
**Azure 預留虛擬機器實體** | 指定[預留實例](https://azure.microsoft.com/pricing/reserved-vm-instances/),以便評估中的成本估計考慮它們。<br/><br/> Azure 遷移目前僅支援"現付即付"服務的 Azure 預留 VM 實例。
**調整標準大小** | 用於調整 Azure VM 的大小。<br/><br/> 使用按「大小調整」或基於性能的尺寸調整。
**效能歷程記錄** | 與基於性能的尺寸調整一起使用。 性能歷史記錄指定計算性能數據時使用的持續時間。
**百分位數使用率** | 與基於性能的尺寸調整一起使用。 百分位數利用率指定用於許可權化的性能示例的百分位數值。
**VM 系列** | 要考慮進行許可權調整的 Azure VM 系列。 例如,如果 Azure 中沒有需要 A 系列 VM 的生產環境,則可以從系列清單中排除 A 系列。
**緩和因數** | 評估期間使用的緩衝區。 它應用於 VM 的 CPU、RAM、磁碟和網路利用率數據。 它考慮了季節性使用、性能歷史記錄短以及未來使用量可能增加等問題。<br/><br/> 例如,利用率為 20% 的 10 核 VM 通常會導致雙核 VM。 舒適係數為 2.0 時,結果改為四核 VM。
**供應項目** | 註冊的[Azure 產品/服務](https://azure.microsoft.com/support/legal/offer-details/)。 伺服器評估估計該產品/服務的成本。
**貨幣** | 帳戶的帳單貨幣。
**折扣 (%)** | 在 Azure 產品/服務頂部收到的任何特定於訂閱的折扣。 預設設定為 0%。
**VM 運作時間** | Azure VM 的持續時間(以每月天數和每天小時數表示)不會連續運行。 成本估算基於該持續時間。<br/><br/> 默認值為每月 31 天和每天 24 小時。
**Azure Hybrid Benefit** | 指定您是否具有軟體保證並有資格享受[Azure 混合權益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 如果該設置的預設值為"是",則 Windows VM 將考慮 Windows VM 以外的作業系統的 Azure 價格。

[查看](best-practices-assessment.md)使用伺服器評估創建評估的最佳做法。

## <a name="calculate-readiness"></a>計算就緒情況

並非所有計算機都適合在 Azure 中運行。 伺服器評估評估所有本地計算機,並分配給它們一個就緒類別。

- **已為 Azure 做好準備**:可以根據需要將電腦遷移到 Azure,而無需進行任何更改。 它將在 Azure 中開始,支援 Azure。
- **有條件地為 Azure 做好準備**:電腦可能在 Azure 中啟動,但可能沒有完整的 Azure 支援。 例如,Azure 不支援運行舊版本的 Windows 伺服器的電腦。 在將這些計算機遷移到 Azure 之前,必須小心謹慎。 要修復任何就緒問題,請遵循評估建議的補救指南。
- **尚未準備好使用 Azure**:計算機不會在 Azure 中啟動。 例如,如果本地電腦的磁碟存儲超過 64 TB,則 Azure 無法承載該電腦。 按照修正指南在遷移之前解決問題。
- **就緒未知**:由於元數據不足,Azure 遷移無法確定計算機的就緒情況。

為了計算就緒情況,伺服器評估會審核下表中匯總的計算機屬性和操作系統設置。

### <a name="machine-properties"></a>機器屬性

伺服器評估會檢查本地 VM 的以下屬性,以確定是否可以在 Azure 上運行。

屬性 | 詳細資料 | Azure 移轉整備程度狀態
--- | --- | ---
**啟動類型** | Azure 支援具有 BIOS 啟動類型的 VM,而不是 UEFI。 | 如果引導類型為 UEFI,則有條件就緒
**核心** | 每台計算機的內核數不得超過 128 個,這是 Azure VM 支援的最大數量。<br/><br/> 如果有提供效能記錄，則 Azure Migrate 會將已使用的核心納入考量，進行比較。 如果評估設置指定舒適系數,則使用的核心數乘以舒適係數。<br/><br/> 如果沒有性能歷史記錄,Azure 遷移將使用分配的內核,而無需應用舒適係數。 | 如果內核數在限制範圍內,則準備就緒
**Ram** | 每台電腦的 RAM 必須不超過 3,892 GB,這是 Azure&nbsp;M 系列的最大大小Standard_M128m<sup>2</sup>個 VM 支援。 [深入了解](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)。<br/><br/> 如果性能歷史記錄可用,Azure 遷移將考慮使用的 RAM 進行比較。 如果指定了舒適系數,則使用的 RAM 乘以舒適係數。<br/><br/> 如果沒有歷史記錄,則無需應用舒適係數即可使用分配的 RAM。<br/><br/> | 如果 RAM 量在限制範圍內,則準備就緒
**存放磁碟** | 磁碟的分配大小不得超過 32 TB。 儘管 Azure 支援具有 Azure 超 SD 磁碟的 64 TB 磁碟,但 Azure 遷移:伺服器評估當前會檢查 32 TB 作為磁碟大小限制,因為它還不支援超 SSD。 <br/><br/> 連接到電腦(包括作業系統磁碟)的磁碟數必須為 65 個或更少。 | 如果磁碟大小和數量在限制範圍內,則準備就緒
**網路功能** | 計算機必須連接不超過 32 個網路介面 (NIC)。 | 如果 NIC 的數量在限制範圍內,則準備就緒

### <a name="guest-operating-system"></a>客體作業系統

除了查看 VM 屬性外,伺服器評估還查看電腦的來賓作業系統,以確定是否可以在 Azure 上運行。

> [!NOTE]
> 要處理 VMware VM 的來賓分析,伺服器評估使用 vCenter 伺服器中為 VM 指定的作業系統。 對於在 VMware 上運行的 Linux VM,伺服器評估目前無法標識來賓作業系統的內核版本。

伺服器評估使用以下邏輯根據作業系統識別 Azure 就緒:

**作業系統** | **詳細資料** | **Azure 移轉整備程度狀態**
--- | --- | ---
Windows Server 2016 與所有 SP | Azure 提供完整支援。 | 已為 Azure 做好準備。
Windows Server 2012 R2 與所有 SP | Azure 提供完整支援。 | 已為 Azure 做好準備。
Windows Server 2012 與所有 SP | Azure 提供完整支援。 | 已為 Azure 做好準備。
Windows Server 2008 R2，含所有 SP | Azure 提供完整支援。| 已為 Azure 做好準備。
Windows Server 2008 (32 位元和 64 位元) | Azure 提供完整支援。 | 已為 Azure 做好準備。
Windows 伺服器 2003 與 Windows 伺服器 2003 R2 | 這些作業系統已過了其終止支援日期,並且需要[自定義支援協定 (CSA)](https://aka.ms/WSosstatement)來在 Azure 中提供支援。 | 有條件地為 Azure 做好準備。 請考慮在遷移到 Azure 之前升級作業系統。
Windows 2000、Windows 98、Windows 95、Windows NT、Windows 3.1 和 MS-DOS | 這些作業系統已超過其支持終止日期。 計算機可能在 Azure 中啟動,但 Azure 不提供作業系統支援。 | 有條件地為 Azure 做好準備。 我們建議您在遷移到 Azure 之前升級作業系統。
Windows 7、Windows 8 和 Windows 10 | Azure[僅通過可視化工作室訂閱提供支援。](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | 有條件地為 Azure 做好準備。
Windows 10 Pro | Azure 對[多租用戶主機權限](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)提供支援。 | 有條件地為 Azure 做好準備。
視窗Vista和視窗XP專業 | 這些作業系統已超過其支持終止日期。 計算機可能在 Azure 中啟動,但 Azure 不提供作業系統支援。 | 有條件地為 Azure 做好準備。 我們建議您在遷移到 Azure 之前升級作業系統。
Linux | 請參考 Azure 支援的[Linux 作業系統](../virtual-machines/linux/endorsed-distros.md)。 其他 Linux 作業系統可能在 Azure 中啟動。 但是,我們建議您在遷移到 Azure 之前將作業系統升級到支援的版本。 | 如果版本受到認同，則可供 Azure 使用。<br/><br/>如果版本未得到認可,則有條件地準備就緒。
其他操作系統,如甲骨文索拉裡斯,蘋果macOS和FreeBSD | Azure 並未認可這些作業系統。 計算機可能在 Azure 中啟動,但 Azure 不提供作業系統支援。 | 有條件地為 Azure 做好準備。 我們建議您在遷移到 Azure 之前安裝受支援的作業系統。  
在 vCenter Server 中作業系統指定為**其他** | 在這種情況下,Azure 遷移無法標識操作系統。 | 整備程度未知。 確保 Azure 支援在 VM 內運行的作業系統。
32 位元作業系統 | 計算機可能在 Azure 中啟動,但 Azure 可能無法提供完全支援。 | 有條件地為 Azure 做好準備。 在遷移到 Azure 之前,請考慮升級到 64 位元作業系統。

## <a name="calculating-sizing"></a>計算大小調整

在電腦標記為已準備好 Azure 後,伺服器評估會制定大小調整建議。 這些建議標識 Azure VM 和磁碟 SKU。 大小調整計算取決於您使用的是本地大小調整還是基於性能的尺寸調整。

### <a name="calculate-sizing-as-is-on-premises"></a>計算大小調整(依本地大小調整)

 如果使用 as-is 本地大小調整,伺服器評估不考慮 VM 和磁碟的性能歷史記錄。

- **計算大小調整**:伺服器評估根據本地分配的大小分配 Azure VM SKU。
- **儲存和磁碟大小調整**:伺服器評估查看評估屬性中指定的存儲類型,並建議適當的磁碟類型。 可能的儲存類型包括標準硬碟、標準 SSD 和進階儲存。 預設儲存類型為「高級」。
- **網路大小調整**:伺服器評估考慮本地電腦上的網路適配器。

### <a name="calculate-sizing-performance-based"></a>計算大小調整(基於效能)

如果使用基於性能的尺寸調整,伺服器評估會按如下方式調整大小建議:

- 伺服器評估會考慮電腦的性能歷史記錄,以標識 Azure 中的 VM 大小和磁碟類型。
- 如果使用 CSV 檔案匯入伺服器,則使用您指定的值。 如果過度分配了本地電腦,利用率較低,並且希望調整 Azure VM 的大小以節省成本,則此方法特別有用。
- 如果不想使用性能數據,請將大小調整條件重置為"本地原樣",如上一節所述。

#### <a name="calculate-storage-sizing"></a>計算儲存大小

對於存儲大小調整,Azure 遷移嘗試將連接到計算機的每個磁碟映射到 Azure 磁碟。 大小調整的工作原理如下:

1. 伺服器評估添加磁碟的讀取和寫入IOPS,以獲得所需的總IOPS。 同樣,它添加讀寫輸送量值,以獲得每個磁碟的總輸送量。
1. 如果將儲存類型指定為自動儲存類型,則所選類型基於有效的 IOPS 和輸送量值。 伺服器評估確定是將磁碟映射到 Azure 中的標準硬碟、標準 SSD 還是進階磁碟。 如果存儲類型設置為這些磁碟類型之一,伺服器評估將嘗試在所選存儲類型中找到磁碟 SKU。
1. 選擇磁碟如下:
    - 如果伺服器評估找不到具有所需 IOPS 和輸送量的磁碟,則它將電腦標記為不適合 Azure。
    - 如果伺服器評估找到一組合適的磁碟,它將選擇支援評估設置中指定位置的磁碟。
    - 如果有多個符合條件的磁碟,伺服器評估會選擇成本最低的磁碟。
    - 如果任何磁碟的性能數據不可用,則配置磁碟大小用於在 Azure 中查找標準 SSD 磁碟。

#### <a name="calculate-network-sizing"></a>計算網路大小

伺服器評估嘗試尋找連接到本地電腦的網路配接器的數量和所需性能的 Azure VM。

- 為了獲得本地 VM 的有效網路性能,伺服器評估將數據傳輸速率聚合到電腦(網路外)中,並跨所有網路適配器進行。 然後應用舒適係數。 它使用生成的值尋找支援所需網路性能的 Azure VM。
- 除了網路性能外,伺服器評估還考慮 Azure VM 是否可以支援所需的網路適配器數。
- 如果網路性能數據不可用,伺服器評估將僅考慮 VM 大小調整的網路適配器計數。

#### <a name="calculate-compute-sizing"></a>計算大小調整

計算存儲和網路要求後,伺服器評估會考慮 CPU 和 RAM 要求,以在 Azure 中找到合適的 VM 大小。

- Azure 遷移查看有效利用的核心和 RAM,以查找合適的 Azure VM 大小。
- 如果找不到合適的大小，便會將機器標示為不適合 Azure。
- 如果找到合適的大小，Azure Migrate 會套用儲存體與網路計算。 然後,它將為最終 VM 大小建議應用位置和定價層設置。
- 如果有多個符合資格的 Azure VM 大小，建議使用成本最低的那個。

## <a name="confidence-ratings-performance-based"></a>建信度(基於績效)

Azure 遷移中的每個基於性能的評估都與置信度評級相關聯。 額定值範圍從一顆(最低)到五顆(最高)星。 置信度可幫助您估計 Azure 遷移提供的大小建議的可靠性。

- 置信度評級分配給評估。 評級基於計算評估所需的數據點的可用性。
- 對於基於性能的尺寸調整,伺服器評估需要:
    - CPU 和 VM RAM 的利用率數據。
    - 連接到 VM 的每個磁碟的磁碟 IOPS 和輸送量數據。
    - 網路 I/O,用於處理連接到 VM 的每個網路適配器的基於性能的尺寸調整。

如果這些利用率編號中的任何一個不可用,則大小建議可能不可靠。

> [!NOTE]
> 對於使用導入的 CSV 檔評估的伺服器,不會分配置信度。 評級也不適用於本地評估。

### <a name="ratings"></a>評等

下表顯示了評估置信度,該評級取決於可用數據點的百分比:

   **資料點的可用性** | **信賴評等**
   --- | ---
   0-20% | 1 顆星
   21-40% | 2 顆星
   41-60% | 3 顆星
   61-80% | 4 顆星
   81-100% | 5 顆星

### <a name="low-confidence-ratings"></a>低置信度

以下是評估可能獲得低置信度的幾個原因:

- 在建立評估的持續時間內,您沒有分析您的環境。 例如,如果創建評估時,性能持續時間設置為一天,則必須在開始發現要收集的所有數據點後至少等待一天。
- 在計算評估期間,某些 VM 被關閉。 如果任何 VM 在一段時間內關閉,伺服器評估無法收集該期間的性能數據。
- 在計算評估期間創建了一些 VM。 例如,假設您為上個月的績效歷史記錄創建了評估,但某些 VM 是在一周前創建的。 新 VM 的性能歷史記錄將不存在整個持續時間。

> [!NOTE]
> 如果任何評估的置信度低於五星,我們建議您至少等待一天,讓產品分析環境,然後重新計算評估。 否則,基於性能的尺寸調整可能不可靠。 在這種情況下,我們建議您將評估切換到本地大小調整。

## <a name="calculate-monthly-costs"></a>計算每月成本

調整大小建議完成後,Azure 遷移計算遷移后的計算和存儲成本。

- **計算成本**:Azure 遷移使用建議的 Azure VM 大小和 Azure 計費 API 來計算 VM 的每月成本。

    計算考慮:
    - 作業系統
    - 軟體保證
    - 保留執行個體
    - VM 運作時間
    - Location
    - 貨幣設定

    伺服器評估聚合所有計算機的成本,以計算每月計算總成本。

- **存儲成本**:機器的每月存儲成本是通過聚合連接到計算機的所有磁碟的每月成本來計算的。

    伺服器評估通過聚合所有計算機的儲存成本來計算每月總存儲成本。 目前,計算不考慮評估設置中指定的報價。

成本會以評量設定中指定的貨幣顯示。

## <a name="next-steps"></a>後續步驟

[查看](best-practices-assessment.md)創建評估的最佳做法。 

- 瞭解[VMware VM、](tutorial-prepare-vmware.md)[超 V VM](tutorial-prepare-hyper-v.md)和[實體伺服器](tutorial-prepare-physical.md)的運行評估。
- 瞭解如何評估[使用 CSV 檔導入的](tutorial-assess-import.md)伺服器。
- 瞭解如何設定[相依項以檢視化](concepts-dependency-visualization.md)。
