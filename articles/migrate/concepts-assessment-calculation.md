---
title: Azure 移植伺服器評估中的評估
description: 瞭解 Azure 移植伺服器評估中的評估
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: d1f32eea0ec6a8a4877fd1dc134344cfe68dcaba
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537758"
---
# <a name="assessments-in-azure-migrateserver-assessment"></a>Azure 移轉中的評估:伺服器評估

本文在[Azure 遷移:伺服器評估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具中概述了評估。 伺服器評估工具可以評估本地 VMware VM、超 VM 和實體伺服器,以便遷移到 Azure。

## <a name="whats-an-assessment"></a>什麼是評估?

使用「伺服器評估」工具進行評估可測量將本地伺服器遷移到 Azure 的就緒情況並估計影響。

> [!NOTE]
> 在 Azure 政府中,查看[支持的目標](migrate-support-matrix.md#supported-geographies-azure-government)評估位置。 請注意,評估中的 VM 大小建議將專門用於政府雲區域的 VM 系列。 [瞭解有關](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines)VM 類型的資訊。

## <a name="types-of-assessments"></a>評估類型

使用伺服器評估創建的評估是數據的時間點快照。 伺服器評估提供兩種類型的評估。

**評量類型** | **詳細資料** | **Data**
--- | --- | ---
**以效能為基礎** | 根據收集的績效資料提出建議的評估 | VM 大小建議基於 CPU 和記憶體利用率數據。<br/><br/> 磁碟型態建議(標準 HDD/SSD 或進階管理磁碟)基於 IOPS 和本地磁碟的輸送量。
**本地端** | 不使用性能數據提出建議的評估。 | VM 大小建議基於本地端 VM 大小<br/><br> 建議的磁碟類型基於評估的選定存儲類型。

## <a name="how-do-i-run-an-assessment"></a>如何運行評估?

執行評估的方法有幾種:

- 使用輕量級 Azure 遷移設備收集的伺服器元數據評估計算機。 設備發現本地電腦,並將計算機中繼資料/性能資料發送到 Azure 遷移。
- 使用以逗號分隔值 (CSV) 格式導入的伺服器元數據評估電腦。

## <a name="how-do-i-assess-with-the-appliance"></a>如何使用產品進行評估?

如果要部署 Azure 移轉裝置以發現本地伺服器,請執行以下操作:

1. 將 Azure 和本地環境設置為使用伺服器評估。
2. 對於第一次評估,請創建 Azure 專案,並將伺服器評估工具添加到其中。
3. 部署輕量級 Azure 遷移設備。 設備不斷發現本地電腦,並將計算機中數據和性能數據發送到 Azure 遷移。 設備部署為 VM 或物理電腦。 無需在要評估的電腦上安裝任何內容。
4. 設備開始發現機器后,您可以將要評估的計算機收集到組中,並為組運行評估。

您可以按照我們的[VMware、Hyper-V](tutorial-prepare-vmware.md)或[物理伺服器](tutorial-prepare-physical.md)教程來試用[Hyper-V](tutorial-prepare-hyper-v.md)這些步驟。

## <a name="how-do-i-assess-with-imported-data"></a>如何使用導入的數據進行評估?

如果您正在使用 CSV 檔評估伺服器,則不需要設備。 相反,您可以執行以下操作:

1. 將 Azure 設置為使用伺服器評估。
2. 對於第一次評估,請創建 Azure 專案,並將伺服器評估工具添加到其中。
3. 下載 CSV 樣本,並將伺服器數據添加到其中。
4. 將範本導入伺服器評估。
5. 發現隨導入一起添加的伺服器,然後收集到組中,併為該組運行評估。

## <a name="what-data-does-the-appliance-collect"></a>產品收集哪些數據?

如果使用 Azure 遷移設備進行評估,請瞭解為[VMware](migrate-appliance.md#collected-data---vmware)和[Hyper-V](migrate-appliance.md#collected-data---hyper-v)收集的中繼資料和效能數據。

## <a name="how-does-the-appliance-calculate-performance-data"></a>設備如何計算性能數據?

如果使用裝置進行發現,則計算設置的性能數據將收集如下:

1. 產品收集即時取樣點:

    - **VMware VM:** 設備每 20 秒收集一個即時採樣點。
    - **Hyper-V VM**:即時採樣點每 30 秒收集一次。
    - **物理伺服器**:實時採樣點每五分鐘收集一次。 
    
2. 產品將採樣點(20 秒、30 秒、5 分鐘)匯總一次,每 10 分鐘創建一個數據點。 要創建單點,設備將從所有示例中選擇峰值,然後將其發送到 Azure。
3. 伺服器評估存儲上個月的所有 10 分鐘採樣點。
4. 創建評估時,伺服器評估會根據*性能歷史記錄*和*百分位數利用率*的百分位數值標識用於正確調整大小的適當數據點。

    - 例如,如果性能歷史記錄設置為一周,並且百分位數利用率是第 95 百分位,則伺服器評估會按升序對上周的 10 分鐘採樣點進行排序,並選擇右調整大小的第 95 個百分位數值。 
    - 第 95 個百分位值可確保忽略任何異常值,如果選擇第 99 個百分位,可能會包括這些異常值。
    - 如果要選擇期間的峰值使用率,並且不想錯過任何異常值,則應選擇百分位數利用率的第 99 百分位數。

5. 此值乘以舒適係數,以獲得設備收集的每個指標的有效性能利用率數據(CPU 利用率、記憶體利用率、磁碟 IOPS(讀取和寫入)、磁碟輸送量(讀取和寫入)和網路輸送量(進出)。



## <a name="how-are-assessments-calculated"></a>如何計算評估? 

伺服器評估中的評估使用本地計算機的中繼資料/性能資料進行計算。 如果部署 Azure 遷移設備,則使用設備收集的數據進行評估。 如果對使用 導入的計算機運行評估。CSV 檔,提供計算的中繼資料。 計算分三階段:

1. **計算 Azure 就緒性**:評估電腦是否適合遷移到 Azure。
2. **計算大小調整建議**:估計計算、存儲和網路大小調整。 
2. **計算每月成本**:計算遷移后在 Azure 中運行計算機的估計每月計算和儲存成本。

計算順序,並且計算機伺服器僅在通過前一個階段時才移動到稍後階段。 例如,如果伺服器未通過 Azure 就緒,則將其標記為不適合 Azure,並且不會為該伺服器執行大小調整和成本計算。


## <a name="whats-in-an-assessment"></a>評估包含什麼？

以下是伺服器評估中評估中包含的內容。

**屬性** | **詳細資料**
--- | ---
**目標位置** | 要遷移到的位置。伺服器評估目前支援這些目標 Azure 區域:<br/><br/> 澳大利亞東部、澳大利亞東南部、巴西南部、加拿大中部、加拿大東部、印度中部、美國中部、中國東部、中國北部、東亞、美國東部、美國東部、德國中部、德國東北部、日本東部、日本西部、韓國中部、韓國中部、美國中北部、北歐、美國中南部、東南亞、南印度、英國南部、英國西部、美國亞利桑那州州長、美國德克薩斯州州長、美國弗吉尼亞州州長,美國中西部、西歐、西印度、美國西部和美國西部2。
*目標儲存磁碟(大小調整)** | 用於在 Azure 中儲存的磁碟的類型。 <br/><br/> 將目標儲存磁碟指定為進階託管磁碟、標準 SSD 託管磁碟或標準 HDD 託管磁碟。
**目標儲存磁碟(基於效能的大小調整)** | 將目標儲存磁碟的類型指定為自動、進階託管、標準硬碟託管或標準 SSD 託管。<br/><br/> **自動**:磁碟建議基於磁碟的性能數據(每秒輸入/輸出操作 (IOPS) 和輸送量)。<br/><br/>**進階/標準**:評估建議在所選存儲類型內使用磁碟 SKU。<br/><br/> 如果要實現單個實例 VM SLA 的 99.9%,則考慮使用高級託管磁碟。 這可確保建議將評估中的所有磁碟作為高級管理磁碟。<br/><br/> Azure Migrate 只支援將受控磁碟用於進行移轉評估。
**保留實體 (R)** | 在 Azure 中指定[預留實例](https://azure.microsoft.com/pricing/reserved-vm-instances/),以便評估中的成本估計會考慮 RI 折扣。<br/><br/> 當前僅支援 Azure 遷移中的即用即付產品/服務。
**調整標準大小** | 用於在 Azure 中調整 VM 的大小。<br/><br/> 使用按大小調整或基於性能的尺寸調整。
**效能歷程記錄** | 與基於性能的尺寸調整一起使用。 指定評估性能數據時使用的持續時間。
**百分位數使用率** | 與基於性能的尺寸調整一起使用。 指定用於右大小調整的性能範例的百分位數值。 
**VM 系列** | 指定要考慮調整大小的 Azure VM 系列。 例如,如果 Azure 中沒有需要 A 系列 VM 的生產環境,則可以從清單或系列中排除 A 系列。
**緩和因數** | 評估期間使用的緩衝區。 應用於 VM(CPU、記憶體、磁碟和網路)的電腦利用率數據之上。 它考慮了季節性使用、性能歷史記錄短以及未來使用量可能增加等問題。<br/><br/> 例如,利用率為 20% 的 10 核 VM 通常會導致雙核 VM。 舒適係數為 2.0 倍,其結果是四核 VM。
**供應項目** | 顯示您註冊的[Azure 產品/服務](https://azure.microsoft.com/support/legal/offer-details/)。 伺服器評估相應地估計成本。
**貨幣** | 為您的帳戶計費幣種。
**折扣 (%)** | 列出您在 Azure 產品/服務頂部收到的任何特定於訂閱的折扣。 預設設定為 0%。
**VM 運作時間** | 如果 Azure VM 不會每周 7 天、每天 24 小時運行,則可以指定它們將運行的持續時間(每月天數和小時)。 成本估算將相應地處理。<br/><br/> 預設值是每月 31 天和每天 24 小時。
**Azure Hybrid Benefit** | 指定您是否具有軟體保證並有資格享受[Azure 混合權益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 如果設置為「是」(預設設置),則 Windows VM 會考慮非 Windows Azure 價格。

[查看](best-practices-assessment.md)使用伺服器評估創建評估的最佳做法。


## <a name="calculate-readiness"></a>計算就緒情況

並非所有計算機都適合在 Azure 中運行。 伺服器評估評估每個本地計算機,並將其分配為就緒類別。 
- **已為 Azure 做好準備**:可以根據需要將電腦遷移到 Azure,而無需進行任何更改。 它將在 Azure 中開始,支援 Azure。
- **有條件地為 Azure 做好準備**:電腦可能在 Azure 中啟動,但可能沒有完整的 Azure 支援。 例如,Azure 不支援運行舊版本的 Windows Server 的電腦。 在將這些計算機遷移到 Azure 之前,必須小心謹慎。 按照評估中建議的補救指南進行修復,以修復就緒問題。
- **尚未準備好使用 Azure**:計算機不會在 Azure 中啟動。 例如,如果本地電腦磁碟超過 64 TB,則無法將其託管在 Azure 中。 按照修正指南在遷移之前修復問題。 
- **就緒性未知**:由於元數據不足,Azure 遷移無法確定計算機的就緒情況。

為了計算就緒情況,伺服器評估會審核下表中匯總的計算機屬性和操作系統設置。 

### <a name="machine-properties"></a>機器屬性

伺服器評估會檢查本地 VM 的以下屬性,以確定是否可以在 Azure 上運行。

**屬性** | **詳細資料** | **Azure 移轉整備程度狀態**
--- | --- | ---
**啟動類型** | Azure 支援具有 BIOS 啟動類型的 VM,而不是 UEFI。 | 如果引導類型為 UEFI,則有條件就緒。
**核心** | 計算機中的內核數必須等於或小於 Azure VM 支援的最大內核數 (128)。<br/><br/> 如果有提供效能記錄，則 Azure Migrate 會將已使用的核心納入考量，進行比較。 如果評量設定已指定緩和因數，則會將使用的核心數目乘以緩和因數。<br/><br/> 如果沒有性能歷史記錄,Azure 遷移將使用分配的內核,而無需應用舒適係數。 | 小於或等於限制便就緒。
**記憶體** | 計算機記憶體大小必須等於或小於 Azure VM 允許的最大記憶體(Azure M&nbsp;系列Standard_M128m<sup>2</sup>上 3892 GB)。 [深入了解](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)。<br/><br/> 如果有提供效能記錄，Azure Migrate 會將已使用的記憶體納入考量，進行比較。 如果已指定緩和因數，則會將使用的記憶體乘以緩和因數。<br/><br/> 如果沒有歷史記錄,則不使用分配記憶體,而不應用舒適係數。<br/><br/> | 在限制內便就緒。
**存放磁碟** | 磁碟的分配大小必須為 32 TB 或更少。 儘管 Azure 支援具有超 SSD 磁碟的 64 TB 磁碟,但 Azure 遷移:伺服器評估當前會檢查 32 TB 作為磁碟大小限制,因為它還不支援超 SD。 <br/><br/> 連接到電腦的磁碟數必須為 65 個或更少,包括作業系統磁碟。 | 在限制內便就緒。
**網路功能** | 計算機必須連接 32 個或更少的網路介面 (NIC)。 | 在限制內便就緒。

### <a name="guest-operating-system"></a>客體作業系統
除了 VM 屬性外,伺服器評估還查看電腦的來賓作業系統,以確定是否可以在 Azure 上運行。

> [!NOTE]
> 對於 VMware VM,伺服器評估使用為 vCenter 伺服器中為 VM 指定的作業系統來處理來賓作業系統分析。 對於在 VMware 上運行的 Linux VM,它目前無法標識來賓作業系統的確切內核版本。

伺服器評估使用以下邏輯來根據操作系統標識 Azure 就緒狀態。

**作業系統** | **詳細資料** | **Azure 移轉整備程度狀態**
--- | --- | ---
Windows Server 2016 與所有 SP | Azure 提供完整支援。 | 可供 Azure 使用
Windows Server 2012 R2 與所有 SP | Azure 提供完整支援。 | 可供 Azure 使用
Windows Server 2012 與所有 SP | Azure 提供完整支援。 | 可供 Azure 使用
Windows Server 2008 R2，含所有 SP | Azure 提供完整支援。| 可供 Azure 使用
Windows Server 2008 (32 位元和 64 位元) | Azure 提供完整支援。 | 可供 Azure 使用
Windows Server 2003、2003 R2 | 這些作業系統已過了其終止支援日期,並且需要[自定義支援協定 (CSA)](https://aka.ms/WSosstatement)來在 Azure 中提供支援。 | 有條件地為 Azure 做好準備。 請考慮在遷移到 Azure 之前升級作業系統。
Windows 2000、98、95、NT、3.1、MS-DOS | 這些操作系統已過了其支持終止日期。 計算機可能在 Azure 中啟動,但 Azure 不提供作業系統支援。 | 有條件地為 Azure 做好準備。 我們建議您在遷移到 Azure 之前升級作業系統。
Windows Client 7、8 及 10 | Azure [僅對 Visual Studio 訂用帳戶](https://docs.microsoft.com/azure/virtual-machines/windows/client-images)提供支援。 | 可有條件地供 Azure 使用
Windows 10 專業版桌面 | Azure 對[多租用戶主機權限](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)提供支援。 | 可有條件地供 Azure 使用
Windows Vista、XP Professional | 這些操作系統已過了其支持終止日期。 計算機可能在 Azure 中啟動,但 Azure 不提供作業系統支援。 | 有條件地為 Azure 做好準備。 我們建議您在遷移到 Azure 之前升級作業系統。
Linux | Azure 認同這些 [Linux 作業系統](../virtual-machines/linux/endorsed-distros.md)。 其他 Linux 作業系統可能在 Azure 中啟動,但我們建議您在遷移到 Azure 之前將作業系統升級到支援的版本。 | 如果版本受到認同，則可供 Azure 使用。<br/><br/>如果版本未受到認同，則有條件地可供使用。
其他作業系統<br/><br/> 例如,甲骨文索拉裡斯,蘋果macOS等,FreeBSD等。 | Azure 並未認可這些作業系統。 計算機可能在 Azure 中啟動,但 Azure 不提供作業系統支援。 | 有條件地為 Azure 做好準備。 我們建議您在遷移到 Azure 之前安裝受支援的作業系統。  
在 vCenter Server 中作業系統指定為**其他** | Azure Migrate 無法在此情況下識別作業系統。 | 整備程度未知。 請確保 Azure 支援 VM 內執行的作業系統。
32 位元作業系統 | 計算機可能在 Azure 中啟動,但 Azure 可能無法提供完全支援。 | 有條件地為 Azure 做好準備。 在遷移到 Azure 之前,請考慮將電腦的作業系統從 32 位元作業系統升級到 64 位元作業系統。

## <a name="calculating-sizing"></a>計算大小調整


在電腦標記為已準備好 Azure 後,伺服器評估會制定大小調整建議以標識 Azure VM 和磁碟 SKU。 大小調整計算取決於您使用的是本地大小調整還是基於性能的尺寸調整。

### <a name="calculate-sizing-as-is-on-premises"></a>計算大小調整(依本地大小調整)

 如果使用 as-is 本地大小調整,伺服器評估不考慮 VM 和磁碟的性能歷史記錄。

- **計算大小調整**:它根據在本地分配的大小分配 Azure VM SKU。
- **儲存/磁碟大小調整**:伺服器評估查看評估屬性(標準 HDD/SSD/進階)中指定的儲存類型,並相應地推薦磁碟類型。 預設儲存類型為高級磁碟。
- **網路大小調整**:伺服器評估考慮本地電腦上的網路適配器。


### <a name="calculate-sizing-performance-based"></a>計算大小調整(基於效能)

如果使用基於性能的尺寸調整,伺服器評估會按如下方式調整大小建議:

- 伺服器評估會考慮電腦的性能歷史記錄,以標識 Azure 中的 VM 大小和磁碟類型。
- 如果已使用 CSV 檔匯入伺服器,則使用您指定的值。 如果過度分配了本地電腦,利用率較低,並且希望調整 Azure 中的 VM 大小以節省成本,則此方法特別有用。 
- 如果不想使用性能數據,請將大小調整條件重置為"本地原樣",如上一節所述。

#### <a name="calculate-storage-sizing"></a>計算儲存大小

對於儲存大小調整,Azure 遷移嘗試將附加到電腦的每個磁碟映射到 Azure 中的磁碟,其工作方式如下:

1. 伺服器評估添加磁碟的讀取和寫入IOPS,以獲得所需的總IOPS。 同樣,它添加讀寫輸送量值,以獲得每個磁碟的總輸送量。
2. 如果已根據有效的 IOPS 和輸送量值將儲存類型指定為"自動",則伺服器評估將確定磁碟應映射到 Azure 中的標準 HDD、標準 SSD 或高級磁碟。 如果儲存類型設定為標準 HDD/SSD/進階,伺服器評估將嘗試在所選儲存類型(標準硬碟/SSD/進階磁碟)中找到磁碟 SKU。
3. 選擇磁碟如下:
    - 如果伺服器評估找不到具有所需 IOPS 和輸送量的磁碟,則它將電腦標記為不適合 Azure。
    - 如果伺服器評估找到一組合適的磁碟,它將選擇支援評估設置中指定位置的磁碟。
    - 如果有多個符合條件的磁碟,伺服器評估會選擇成本最低的磁碟。
    - 如果任何磁碟的性能數據不可用,則磁碟的配置數據(磁碟大小)用於在 Azure 中尋找標準 SSD 磁碟。

#### <a name="calculate-network-sizing"></a>計算網路大小

伺服器評估嘗試尋找 Azure VM,該 VM 可以支援連接到本地電腦的網路配接器數量以及這些網路適配器所需的效能。
- 為了獲得本地 VM 的有效網路性能,伺服器評估將每秒傳輸的數據 (MBps) 聚合到電腦(網路外),跨所有網路適配器,並應用舒適係數。 它使用此編號尋找可支援所需網路性能的 Azure VM。
- 除了網路性能外,伺服器評估還考慮 Azure VM 是否可以支援所需的網路適配器數。
- 如果沒有可用的網路性能數據,伺服器評估將僅考慮 VM 大小調整的網路適配器計數。


#### <a name="calculate-compute-sizing"></a>計算大小調整

計算存儲和網路要求後,伺服器評估會考慮 CPU 和記憶體要求,以在 Azure 中找到合適的 VM 大小。
- Azure 遷移查看有效利用的核心和記憶體,以在 Azure 中找到合適的 VM 大小。
- 如果找不到合適的大小，便會將機器標示為不適合 Azure。
- 如果找到合適的大小，Azure Migrate 會套用儲存體與網路計算。 然後,它將為最終 VM 大小建議應用位置和定價層設置。
- 如果有多個符合資格的 Azure VM 大小，建議使用成本最低的那個。


## <a name="confidence-ratings-performance-based"></a>建信度(基於績效)

Azure 遷移中的每個基於性能的評估都與從一星(最低)到五星(最高)的置信度評級相關聯。 置信度可幫助您估計 Azure 遷移提供的大小建議的可靠性。

- 根據計算評量所需的資料點可用性，每個評量都會指派信賴評等。
- 對於基於性能的尺寸調整,伺服器評估需要:
    - CPU 和 VM 記憶體的利用率數據。
    - 連接到 VM 的每個磁碟的磁碟 IOPS 和輸送量數據。
    - 網路 I/O,用於處理連接到 VM 的每個網路適配器的基於性能的尺寸調整。
    - 如果這些利用率編號中的任何一個不可用,則大小建議可能不可靠。

> [!NOTE]
> 對於使用導入的評估的伺服器,不會分配置信度。CSV 檔。 評級也不適用於本地評估。
   
### <a name="ratings"></a>評等

根據可用數據點的百分比,評估的置信度評級如下。

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
- 部分 VM 在評量計算期間關閉。 如果任何 VM 在一段時間內關閉,伺服器評估無法收集該期間的性能數據。
- 在計算評估期間創建了一些 VM。 例如,如果為上個月的性能歷史記錄創建了評估,但某些 VM 僅在一周前在環境中創建,則新 VM 的性能歷史記錄將不存在整個持續時間。

> [!NOTE]
> 如果任何評估的置信度低於五星,我們建議您至少等待一天,讓產品分析環境,然後重新計算評估。 如果沒有,基於性能的尺寸調整可能不可靠。 在這種情況下,我們建議您將評估切換到本地大小調整。

## <a name="calculate-monthly-costs"></a>計算每月成本

調整大小建議完成後,Azure 遷移計算遷移后的計算和存儲成本。

- **計算成本**：使用建議的 Azure VM 大小時，Azure Migrate 會使用計費 API 來計算虛擬機器的每月成本。
    - 計算時會將作業系統、軟體保證、保留執行個體、VM 運作時間、位置和貨幣設定納入考量。
    - 它聚合所有機器的成本,以計算每月計算總成本。
- **儲存成本**:機器的每月存儲成本通過聚合連接到計算機的所有磁碟的每月成本來計算,如下所示:
    - 伺服器評估通過聚合所有計算機的儲存成本來計算每月總存儲成本。
    - 目前,計算不考慮評估設置中指定的報價。

成本會以評量設定中指定的貨幣顯示。


## <a name="next-steps"></a>後續步驟

[查看](best-practices-assessment.md)創建評估的最佳做法。 


- 瞭解[VMware VM、](tutorial-prepare-vmware.md)[超 V VM](tutorial-prepare-hyper-v.md)和[實體伺服器](tutorial-prepare-physical.md)的運行評估。
- 瞭解如何評估[使用 CSV 檔導入的](tutorial-assess-import.md)伺服器。
- 瞭解如何設定[相依項以檢視化](concepts-dependency-visualization.md)。
