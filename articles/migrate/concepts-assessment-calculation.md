---
title: Azure Migrate 伺服器評量中的 Azure VM 評量
description: 瞭解 Azure Migrate Server 評量中的評量
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 9bdf907ede2c09f7e314df619cd81059956f17dc
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567751"
---
# <a name="server-assessment-overview-migrate-to-azure-vms"></a>伺服器評定總覽 (遷移至 Azure Vm) 

本文提供 [Azure Migrate：伺服器評](migrate-services-overview.md#azure-migrate-server-assessment-tool) 量工具中的評量總覽。 此工具可以評估要遷移至 Azure 的內部部署 VMware 虛擬機器、Hyper-v Vm 和實體伺服器。

## <a name="whats-an-assessment"></a>什麼是評量？

使用伺服器評量工具的評量可測量就緒程度，並評估將內部部署伺服器遷移至 Azure 的影響。

> [!NOTE]
> 在 Azure Government 中，檢查 [支援的目標](migrate-support-matrix.md#supported-geographies-azure-government) 評定位置。 請注意，評量中的 VM 大小建議將專門針對政府雲端區域使用 VM 系列。 [深入瞭解](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) VM 類型。

## <a name="types-of-assessments"></a>評量類型

您可以使用 Azure Migrate：伺服器評量來建立的評量有兩種。

**評量類型** | **詳細資料**
--- | --- 
**Azure VM** | 評估內部部署伺服器，並將其遷移至 Azure 虛擬機器。 <br/><br/> 您可以使用評量類型，評量內部部署 [VMware VM](how-to-set-up-appliance-vmware.md)、[Hyper-V VM](how-to-set-up-appliance-hyper-v.md) 和[實體伺服器](how-to-set-up-appliance-physical.md)以移轉至 Azure。
**Azure VMware 解決方案 (AVS)** | 評估內部部署伺服器，並將其遷移至 [Azure VMware 解決方案 (AVS)](../azure-vmware/introduction.md)。 <br/><br/> 您可以使用此評量類型，評量內部部署 [VMware VM](how-to-set-up-appliance-vmware.md) 以移轉至 Azure VMware 解決方案 (AVS)。[深入了解](concepts-azure-vmware-solution-assessment-calculation.md)

使用伺服器評量建立的評量是資料的時間點快照集。 伺服器評量中的 Azure VM 評量提供兩個調整準則選項：

**評量類型** | **詳細資料** | **Data**
--- | --- | ---
**以效能為基礎** | 根據所收集的效能資料做出建議的評量 | VM 大小建議是以 CPU 和 RAM 使用量資料為基礎。<br/><br/> 磁片類型建議是以每秒的輸入/輸出作業為基礎， (IOPS) 和內部部署磁片的輸送量。 磁片類型為 Azure 標準 HDD、Azure 標準 SSD 和 Azure Premium 磁片。
**依內部部署** | 不使用效能資料來提出建議的評量 | VM 大小建議以內部部署 VM 大小為基礎。<br/><br> 建議的磁片類型是根據選取的儲存體類型進行評量。

## <a name="how-do-i-run-an-assessment"></a>如何? 執行評量？

有幾種方式可以執行評量。

- 使用輕量 Azure Migrate 設備所收集的伺服器中繼資料來評估機器。 設備會探索內部部署機器。 然後，它會將電腦中繼資料和效能資料傳送至 Azure Migrate。
- 使用以逗號分隔值匯入的伺服器中繼資料來評定機器 (CSV) 格式。

## <a name="how-do-i-assess-with-the-appliance"></a>如何? 使用設備進行評估嗎？

如果您要部署 Azure Migrate 設備以探索內部部署伺服器，請執行下列步驟：

1. 設定 Azure 和您的內部部署環境，以使用伺服器評量。
1. 針對您的第一次評量，請建立 Azure 專案，並在其中新增伺服器評量工具。
1. 部署輕量 Azure Migrate 設備。 設備會持續探索內部部署電腦，並將電腦中繼資料和效能資料傳送至 Azure Migrate。 將設備部署為 VM 或實體機器。 您不需要在您想要評估的機器上安裝任何資訊。

在設備開始探索機器之後，您可以將想要評估的機器收集到群組中，並針對具有評量類型 **AZURE VM** 的群組執行評量。

遵循適用于 [VMware](./tutorial-discover-vmware.md)、 [hyper-v](./tutorial-discover-hyper-v.md)或 [實體伺服器](./tutorial-discover-physical.md) 的教學課程，以嘗試執行這些步驟。

## <a name="how-do-i-assess-with-imported-data"></a>如何? 使用匯入的資料來評估嗎？

如果您要使用 CSV 檔案評估伺服器，就不需要設備。 請改為執行下列步驟：

1. 設定 Azure 以使用伺服器評量。
1. 針對您的第一次評量，請建立 Azure 專案，並在其中新增伺服器評量工具。
1. 下載 CSV 範本，並將伺服器資料新增至其中。
1. 將範本匯入到伺服器評量。
1. 探索使用匯入新增的伺服器、將它們收集到群組中，然後針對具有評量類型 **AZURE VM** 的群組執行評量。

## <a name="what-data-does-the-appliance-collect"></a>設備會收集哪些資料？

如果您是使用 Azure Migrate 設備進行評量，請瞭解針對 [VMware](migrate-appliance.md#collected-data---vmware) 和 [hyper-v](migrate-appliance.md#collected-data---hyper-v)收集的中繼資料和效能資料。

## <a name="how-does-the-appliance-calculate-performance-data"></a>設備如何計算效能資料？

如果您使用設備進行探索，則會使用下列步驟收集計算設定的效能資料：

1. 設備會收集即時範例點。

    - **VMware vm**：每隔20秒會收集一個範例點。
    - **Hyper-v vm**：每隔30秒會收集一個範例點。
    - **實體伺服器**：每隔五分鐘會收集一次範例點。

1. 設備會結合範例點，每隔10分鐘建立 VMware 和 Hyper-v 伺服器的單一資料點，實體伺服器每隔5分鐘。 若要建立資料點，設備會選取所有樣本的尖峰值。 然後，它會將資料點傳送至 Azure。
1. 伺服器評量會儲存上個月的10分鐘資料點。
1. 當您建立評量時，伺服器評量會識別適合用於擔心的資料點。 識別是以 *效能歷程記錄* 和 *百分位數使用率* 的百分位數值為基礎。

    - 例如，如果效能歷程記錄為一周，而百分位數使用率為第95個百分位數，伺服器評量會將上周的10分鐘樣本點排序。 它會以遞增順序排序，並挑選第95個百分位數值來進行擔心。
    - 第95個百分位數值可確保您會忽略任何極端值，如果您挑選第99個百分位數，這些極端值可能會包含在內。
    - 如果您想要挑選期間的尖峰使用量，但不想遺漏任何極端值，請選取百分位數使用率的第99百分位數。

1. 此值會乘以緩和因數，以取得設備所收集之這些計量的有效效能使用率資料：

    - CPU 使用率
    - RAM 使用率
    - 磁片 IOPS (讀取和寫入) 
    - 磁片輸送量 (讀取和寫入) 
    -  (in 和 out) 的網路輸送量

## <a name="how-are-azure-vm-assessments-calculated"></a>如何計算 Azure VM 評量？

伺服器評量會使用內部部署電腦的中繼資料和效能資料來計算評量。 如果您部署 Azure Migrate 設備，評量會使用設備所收集的資料。 但是，如果您執行使用 CSV 檔案匯入的評量，則會提供計算的中繼資料。

計算會在下列三個階段中發生：

1. **計算 Azure 就緒** 程度：評定機器是否適合遷移至 azure。
1. **計算大小調整建議**：預估計算、儲存體和網路調整大小。
1. **計算每月成本**：計算遷移後在 Azure 中執行機器的預估每月計算和儲存體成本。

計算順序為先前的順序。 電腦伺服器只會在通過上一個階段時移至較新的階段。 例如，如果伺服器無法通過 Azure 就緒階段，它會標示為不適合 Azure。 不會針對該伺服器進行調整大小和成本計算。

## <a name="whats-in-an-azure-vm-assessment"></a>Azure VM 評量有哪些？

以下是在伺服器評量中的 Azure VM 評量中包含的功能：

**屬性** | **詳細資料**
--- | ---
**目標位置** | 您要遷移至的位置。 伺服器評量目前支援下列目標 Azure 區域：<br/><br/> 澳大利亞東部、澳大利亞東南部、巴西南部、加拿大中部、加拿大東部、印度中部、美國中部、中國東部、中國北部、東亞、美國東部、美國東部2、德國中部、德國東北部、日本東部、日本西部、韓國中部、南韓南部、美國中北部、歐洲北部、美國中南部、東南亞、印度南部、英國南部、英國西部、US Gov 亞利桑那州、US Gov 德克薩斯州、US Gov 維吉尼亞州、美國中西部、西歐、印度西部、美國西部和美國西部2。
**目標儲存體磁片 (依大小調整)** | 要在 Azure 中用來儲存的磁片類型。 <br/><br/> 將目標儲存體磁片指定為高階管理、標準 SSD 管理或標準 HDD 管理。
**目標儲存體磁片 (以效能為基礎的大小調整)** | 將目標儲存體磁片的類型指定為自動、高階管理、標準 HDD 管理或標準 SSD 管理。<br/><br/> **自動**：磁片建議是以磁片的效能資料為基礎，這表示 IOPS 和輸送量。<br/><br/>**Premium 或 Standard**：評量建議選取的儲存體類型內的磁片 SKU。<br/><br/> 如果您想要單一實例 VM 服務等級協定 (SLA) 99.9%，請考慮使用 Premium 受控磁片。 這種使用方式可確保建議將評量中的所有磁片作為 Premium 受控磁片。<br/><br/> Azure Migrate 僅支援用於遷移評量的受控磁片。
**Azure 保留的 VM 執行個體** | 指定 [保留的實例](https://azure.microsoft.com/pricing/reserved-vm-instances/) ，讓評量中的成本估計將它們列入考慮。<br/><br/> 當您選取 [保留實例] 時，[折扣 (% ) ] 和 [VM 執行時間] 屬性都不適用。<br/><br/> Azure Migrate 目前僅支援隨用隨付供應專案 Azure 保留的 VM 執行個體。
**調整大小準則** | 用來時 Azure VM。<br/><br/> 使用依大小調整或以效能為基礎的大小調整。
**效能歷程記錄** | 用於以效能為基礎的大小調整。 效能歷程記錄會指定評估效能資料時所使用的持續時間。
**百分位數使用率** | 用於以效能為基礎的大小調整。 百分位數使用率會指定用於擔心之效能範例的百分位數值。
**VM 系列** | 您要考慮擔心的 Azure VM 系列。 例如，如果您沒有實際執行環境需要 Azure 中的 A 系列 VM，則可以從系列清單中排除 A 系列。
**緩和因數** | 評量期間使用的緩衝區。 適用于 Vm 的 CPU、RAM、磁片和網路資料。 它會將問題（例如季節性使用量、簡短的效能歷程記錄，以及未來的使用方式增加）視為有問題。<br/><br/> 例如，具有20% 使用率的10核心 VM 通常會產生雙核心 VM。 使用2.0 的緩和因素，結果會改為四核心 VM。
**供應項目** | 您所註冊的 [Azure 供應](https://azure.microsoft.com/support/legal/offer-details/) 專案。 伺服器評量會評估該供應項目的成本。
**貨幣** | 您帳戶的帳單貨幣。
**折扣 (%)** | 您在 Azure 供應專案上收到的任何訂用帳戶特定折扣。 預設設定為 0%。
**VM 運作時間** | 不會連續執行的 Azure Vm，每天每月的天數和每日的小時數。 成本預估是以該持續時間為基礎。<br/><br/> 預設值為每個月31天和每天24小時。
**Azure Hybrid Benefit** | 指定您是否擁有軟體保證，並符合 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)資格。 如果設定的預設值為 [是]，則 windows Vm 會考慮 Windows 以外的作業系統的 Azure 價格。
**EA 訂用帳戶** | 指定使用 Enterprise 合約 (EA) 訂用帳戶來進行成本估計。 將適用于訂用帳戶的折扣列入考慮。 <br/><br/> 保留保留實例的設定、折扣 (% ) 和 VM 執行時間屬性及其預設設定。


請參閱使用伺服器評量建立評量[的最佳做法](best-practices-assessment.md)。

## <a name="calculate-readiness"></a>計算就緒程度

並非所有機器都適合在 Azure 中執行。 Azure VM 評量會評估所有的內部部署機器，並為它們指派一個就緒分類。

- **適用于 azure**：機器可以依原樣遷移至 Azure，而不需要進行任何變更。 它會在 Azure 中以完整 Azure 支援開始。
- **有條件地供 azure** 使用：電腦可能會在 azure 中啟動，但可能沒有完整的 Azure 支援。 例如，Azure 不支援執行舊版 Windows Server 的電腦。 將這些機器遷移至 Azure 之前，您必須先小心。 若要修正任何就緒問題，請遵循評定建議的補救指導方針。
- **未準備好用於 azure**：電腦將不會在 azure 中啟動。 例如，如果內部部署機器的磁片儲存超過 64 TB，Azure 就無法裝載該機器。 在遷移之前，請遵循補救指導方針來修正問題。
- **就緒狀態不明**：由於中繼資料不足，Azure Migrate 無法判斷電腦是否就緒。

為了計算就緒程度，伺服器評量會審核下表中摘要說明的電腦屬性和作業系統設定。

### <a name="machine-properties"></a>機器屬性

針對 Azure VM 評量，伺服器評量會檢查內部部署 VM 的下列屬性，以判斷它是否可以在 Azure Vm 上執行。

屬性 | 詳細資料 | Azure 移轉整備程度狀態
--- | --- | ---
**開機類型** | Azure 支援開機類型為 BIOS 而不是 UEFI 的 Vm。 | 如果開機類型為 UEFI，有條件地準備就緒
**核心** | 每部電腦都不能有超過128個核心，這是 Azure VM 支援的最大數目。<br/><br/> 如果有提供效能記錄，則 Azure Migrate 會將已使用的核心納入考量，進行比較。 如果評估設定指定了緩和因素，則會將使用的核心數目乘以緩和因數。<br/><br/> 如果沒有效能歷程記錄，Azure Migrate 會使用配置的核心來套用緩和因數。 | 如果核心數目超過限制，即為就緒
**RAM** | 每部電腦都必須有超過 3892 GB 的 RAM，也就是 Azure M 系列 Standard_M128m &nbsp; <sup>2</sup> VM 支援的大小上限。 [深入了解](../virtual-machines/sizes.md)。<br/><br/> 如果有可用的效能歷程記錄，Azure Migrate 會考慮使用的 RAM 進行比較。 如果指定了緩和因素，則會將使用的 RAM 乘以緩和因數。<br/><br/> 如果沒有記錄，則會使用配置的 RAM 來套用緩和因數。<br/><br/> | 如果 RAM 的數量超過限制，則為就緒
**存放磁碟** | 配置的磁片大小不得超過 32 TB。 雖然 Azure 支援 64-TB 磁片與 Azure Ultra SSD 磁片，但 Azure Migrate：伺服器評量目前會檢查 32 TB 的磁片大小限制，因為它還不支援 Ultra SSD。 <br/><br/> 連接至電腦的磁片數目（包括 OS 磁片）必須是65或更少。 | 如果磁片大小和數目在限制內，則為就緒
**網路功能** | 電腦上 (Nic) 不能超過32個網路介面。 | 如果 Nic 數目超過限制，即準備就緒

### <a name="guest-operating-system"></a>客體作業系統

若為 Azure VM 評量，以及審查 VM 屬性，伺服器評量會查看機器的客體作業系統，以判斷是否可在 Azure 上執行。

> [!NOTE]
> 為了處理 VMware Vm 的來賓分析，伺服器評量會使用 vCenter Server 中為 VM 指定的作業系統。 不過，vCenter Server 不會提供 Linux VM 作業系統的核心版本。 若要探索版本，您必須設定 [應用程式探索](./how-to-discover-applications.md)。 然後，設備會使用您在設定應用程式探索時指定的來賓認證來探索版本資訊。


伺服器評量會使用下列邏輯來識別以作業系統為基礎的 Azure 就緒程度：

**作業系統** | **詳細資料** | **Azure 移轉整備程度狀態**
--- | --- | ---
Windows Server 2016 與所有 SP | Azure 提供完整支援。 | 準備好進行 Azure。
Windows Server 2012 R2 與所有 SP | Azure 提供完整支援。 | 準備好進行 Azure。
Windows Server 2012 與所有 SP | Azure 提供完整支援。 | 準備好進行 Azure。
Windows Server 2008 R2，含所有 SP | Azure 提供完整支援。| 準備好進行 Azure。
Windows Server 2008 (32 位元和 64 位元) | Azure 提供完整支援。 | 準備好進行 Azure。
Windows Server 2003 和 Windows Server 2003 R2 | 這些作業系統已通過支援的結束日期，並需要 [自訂支援合約， (CSA) ](/troubleshoot/azure/virtual-machines/server-software-support) 以取得 Azure 支援。 | 有條件地備妥 Azure。 在遷移至 Azure 之前，請考慮先升級作業系統。
Windows 2000、Windows 98、Windows 95、Windows NT、Windows 3.1 和 MS-DOS | 這些作業系統已經過其支援結束日期。 電腦可能會在 Azure 中啟動，但 Azure 不會提供 OS 支援。 | 有條件地備妥 Azure。 建議您在遷移至 Azure 之前，先升級作業系統。
Windows 7、Windows 8 和 Windows 10 | Azure[僅提供 Visual Studio 訂](../virtual-machines/windows/client-images.md)用帳戶的支援。 | 有條件地備妥 Azure。
Windows 10 Pro | Azure 對[多租用戶主機權限](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md)提供支援。 | 有條件地備妥 Azure。
Windows Vista 和 Windows XP Professional | 這些作業系統已經過其支援結束日期。 電腦可能會在 Azure 中啟動，但 Azure 不會提供 OS 支援。 | 有條件地備妥 Azure。 建議您在遷移至 Azure 之前，先升級作業系統。
Linux | 請參閱 Azure 背書的 [Linux 作業系統](../virtual-machines/linux/endorsed-distros.md) 。 其他 Linux 作業系統可能會在 Azure 中啟動。 但我們建議您在遷移至 Azure 之前，先將作業系統升級為背書版。 | 如果版本受到認同，則可供 Azure 使用。<br/><br/>如果未背書版本，有條件地備妥。
其他作業系統，例如 Oracle Solaris、Apple macOS 和 FreeBSD | Azure 並未認可這些作業系統。 電腦可能會在 Azure 中啟動，但 Azure 不會提供 OS 支援。 | 有條件地備妥 Azure。 建議您在遷移至 Azure 之前，先安裝支援的 OS。  
在 vCenter Server 中作業系統指定為 **其他** | 在此情況下，Azure Migrate 無法識別作業系統。 | 整備程度未知。 確定 Azure 支援在 VM 內執行的作業系統。
32 位元作業系統 | 電腦可能會在 Azure 中啟動，但 Azure 可能無法提供完整的支援。 | 有條件地備妥 Azure。 在遷移至 Azure 之前，請考慮升級至64位作業系統。

## <a name="calculating-sizing"></a>計算大小

將機器標示為準備好進行 Azure 之後，伺服器評量會在 Azure VM 評量中進行大小調整建議。 這些建議會識別 Azure VM 和磁片 SKU。 大小計算取決於您使用的是內部部署大小調整還是以效能為基礎的大小調整。

### <a name="calculate-sizing-as-is-on-premises"></a>依內部部署方式計算 (大小調整) 

 如果您使用內部部署的內部部署調整大小，伺服器評量不會考慮 Azure VM 評量中 Vm 和磁片的效能歷程記錄。

- **計算大小調整**：伺服器評量會根據內部部署所配置的大小來配置 AZURE VM SKU。
- **儲存體和磁片大小調整**：伺服器評量會查看評量屬性中指定的儲存體類型，並建議適當的磁片類型。 可能的儲存類型為標準 HDD、標準 SSD 和 Premium。 預設儲存體類型為 Premium。
- **網路大小調整**：伺服器評量會考慮內部部署機器上的網路介面卡。

### <a name="calculate-sizing-performance-based"></a>計算大小 (以效能為基礎的) 

如果您在 Azure VM 評量中使用以效能為基礎的大小調整，伺服器評量會依照下列方式來調整建議：

- 伺服器評量會考慮機器的效能歷程記錄，以識別 Azure 中的 VM 大小和磁片類型。
- 如果您使用 CSV 檔案匯入伺服器，則會使用您指定的值。 如果您已將內部部署機器過度分配、使用率很低，而且您想要時 Azure VM 來節省成本，此方法特別有用。
- 如果您不想要使用效能資料，請依照上一節所述，將調整大小準則重設為內部部署的。

#### <a name="calculate-storage-sizing"></a>計算儲存體大小調整

針對 Azure VM 評量中的儲存體大小調整，Azure Migrate 會嘗試將連接至機器的每個磁片對應至 Azure 磁片。 調整大小的運作方式如下：

1. 伺服器評量會新增磁片的讀取和寫入 IOPS，以取得所需的 IOPS 總數。 同樣地，它會新增讀取和寫入輸送量值，以取得每個磁片的總輸送量。 如果是以匯入為基礎的評量，您可以選擇提供總 IOPS、總輸送量和總計。 未指定個別磁片設定的已匯入檔案中的磁片。 如果您這麼做，則會略過個別的磁片調整大小，並直接使用提供的資料來計算大小，然後選取適當的 VM SKU。

1. 如果您已將儲存體類型指定為自動，則選取的類型是以有效的 IOPS 和輸送量值為基礎。 伺服器評量會決定是否要將磁片對應至 Azure 中的標準 HDD、標準 SSD 或 Premium 磁片。 如果存放裝置類型設定為其中一種磁片類型，伺服器評量會嘗試在選取的儲存體類型內尋找磁片 SKU。
1. 系統會選取磁片，如下所示：
    - 如果伺服器評定找不到具有所需 IOPS 和輸送量的磁片，則會將機器標示為不適合 Azure。
    - 如果伺服器評量找到一組適當的磁片，則會選取支援評量設定中指定之位置的磁片。
    - 如果有多個合格的磁片，伺服器評量會選取成本最低的磁片。
    - 如果任何磁片的效能資料無法使用，設定磁片大小會用來在 Azure 中尋找標準 SSD 磁片。

#### <a name="calculate-network-sizing"></a>計算網路大小調整

針對 Azure VM 評量，伺服器評量會嘗試尋找 Azure VM，以支援連接至內部部署機器的網路介面卡數量和所需的效能。

- 為了取得內部部署 VM 的有效網路效能，伺服器評量會匯總電腦的資料傳輸速率 (網路輸出) 到所有網路介面卡。 然後，它會套用緩和因數。 它會使用產生的值來尋找可支援所需網路效能的 Azure VM。
- 除了網路效能，伺服器評量也會考慮 Azure VM 是否可支援所需的網路介面卡數目。
- 如果網路效能資料無法使用，伺服器評量只會考慮 VM 大小調整的網路介面卡計數。

#### <a name="calculate-compute-sizing"></a>計算計算大小

在計算儲存體和網路需求之後，伺服器評量會考慮 CPU 和 RAM 需求，以在 Azure 中尋找適當的 VM 大小。

- Azure Migrate 查看有效的使用核心和 RAM，以找出合適的 Azure VM 大小。
- 如果找不到合適的大小，便會將機器標示為不適合 Azure。
- 如果找到合適的大小，Azure Migrate 會套用儲存體與網路計算。 然後，它會套用最終 VM 大小建議的位置和定價層設定。
- 如果有多個符合資格的 Azure VM 大小，建議使用成本最低的那個。

## <a name="confidence-ratings-performance-based"></a>以效能為基礎的)  (信賴評等

Azure Migrate 中每個以效能為基礎的 Azure VM 評量都與信賴評等相關聯。 評等範圍從一 (最低) 到五 (最高) 顆星。 信賴評等可協助您預估 Azure Migrate 所提供之大小建議的可靠性。

- 信賴評等會指派給評量。 評等是以計算評量所需的資料點可用性為基礎。
- 針對以效能為基礎的大小調整，伺服器評量需要：
    - CPU 和 VM RAM 的使用量資料。
    - 連接至 VM 的每個磁片的磁片 IOPS 和輸送量資料。
    - 針對連接至 VM 的每個網路介面卡，處理以效能為基礎的大小調整的網路 i/o。

如果其中有任何使用編號無法使用，則大小建議可能不可靠。

> [!NOTE]
> 信賴評等不會指派給使用匯入的 CSV 檔案進行評定的伺服器。 評等也不適用於內部部署評量。

### <a name="ratings"></a>評等

下表顯示評量信賴評等，取決於可用資料點的百分比：

   **資料點的可用性** | **信賴評等**
   --- | ---
   0-20% | 1 顆星
   21-40% | 2 顆星
   41-60% | 3 顆星
   61-80% | 4 顆星
   81-100% | 5 顆星

### <a name="low-confidence-ratings"></a>低信賴等級

以下是評估可能獲得低信賴評等的一些原因：

- 您未在建立評量的期間分析您的環境。 例如，如果您建立的評量將效能持續時間設定為一天，則在開始探索所有資料點之後，您必須至少等候一天才能收集。
- 評定無法收集評估期間內部分或所有 Vm 的效能資料。 如需高信賴評等，請確定： 
    - Vm 在評量期間已開啟電源
    - 允許埠443上的輸出連接
    - 若為 Hyper-v Vm，則會啟用動態記憶體 
    
    請「重新計算」評量，以反映信賴評等的最新變更。

- 某些 Vm 是在計算評估的時間內建立的。 例如，假設您已建立上個月效能歷程記錄的評量，但有些 Vm 只在一周前建立。 在此情況下，將無法取得新的 VM 在這整段期間內的效能資料，且信賴評等將會偏低。

> [!NOTE]
> 如果任何評量的信賴評等少於五顆星，建議您至少等候一天，讓設備分析環境，然後重新計算評量。 否則，以效能為基礎的大小調整可能不可靠。 在此情況下，我們建議您將評量切換至內部部署調整大小。

## <a name="calculate-monthly-costs"></a>計算每月成本

大小調整建議完成之後，Azure Migrate 中的 Azure VM 評量會計算遷移後的計算和儲存成本。

- **計算成本**： Azure Migrate 使用建議的 azure VM 大小和 AZURE 計費 API 來計算 VM 的每月成本。

    此計算會考慮下列事項：
    - 作業系統
    - 軟體保證
    - 保留執行個體
    - VM 運作時間
    - 位置
    - 貨幣設定

    伺服器評量會匯總所有機器的成本，以計算每月總計算成本。

- **儲存成本**：機器的每月儲存成本是藉由匯總所有連接至機器之磁片的每月成本來計算。

    伺服器評量會匯總所有機器的儲存成本，以計算每月總儲存體成本。 目前，計算不會考慮評量設定中指定的供應專案。

成本會以評量設定中指定的貨幣顯示。

## <a name="next-steps"></a>後續步驟

[檢閱](best-practices-assessment.md)適用於建立評估的最佳做法。 

- 瞭解如何對 [VMware vm](./tutorial-discover-vmware.md)、 [hyper-v vm](./tutorial-discover-hyper-v.md)和 [實體伺服器](./tutorial-discover-physical.md)執行評量。
- 瞭解如何評估 [使用 CSV](./tutorial-discover-import.md)檔案匯入的伺服器。
- 瞭解如何設定相依性 [視覺效果](concepts-dependency-visualization.md)。