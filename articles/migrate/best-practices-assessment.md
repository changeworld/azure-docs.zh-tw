---
title: Azure Migrate 伺服器評量中的評估最佳作法
description: 使用 Azure Migrate Server 評量建立評量的秘訣。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: e007f0272a693f5117b0182dad82de2f4a6e252a
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91576875"
---
# <a name="best-practices-for-creating-assessments"></a>建立評量的最佳做法

[Azure Migrate](./migrate-services-overview.md) 會提供工具中樞，協助您探索和評估應用程式、基礎結構和工作負載，並且將這些項目遷移至 Microsoft Azure。 此中樞包含 Azure Migrate 工具和第三方獨立軟體廠商 (ISV) 供應項目。

本文將摘要說明使用 Azure Migrate Server 評定工具建立評量時的最佳做法。

## <a name="about-assessments"></a>關於評量

您使用 Azure Migrate Server 評量建立的評量是資料的時間點快照集。 您可以使用 Azure Migrate：伺服器評量來建立兩種類型的評定：

**評量類型** | **詳細資料**
--- | --- 
**Azure VM** | 評估內部部署伺服器，並將其遷移至 Azure 虛擬機器。 <br/><br/> 您可以使用評量類型，評量內部部署 [VMware VM](how-to-set-up-appliance-vmware.md)、[Hyper-V VM](how-to-set-up-appliance-hyper-v.md) 和[實體伺服器](how-to-set-up-appliance-physical.md)以移轉至 Azure。 [深入了解](concepts-assessment-calculation.md)
**Azure VMware 解決方案 (AVS)** | 評估內部部署伺服器，並將其遷移至 [Azure VMware 解決方案 (AVS)](../azure-vmware/introduction.md)。 <br/><br/> 您可以使用此評量類型，評量內部部署 [VMware VM](how-to-set-up-appliance-vmware.md) 以移轉至 Azure VMware 解決方案 (AVS)。 [深入了解](concepts-azure-vmware-solution-assessment-calculation.md)


### <a name="sizing-criteria"></a>調整大小準則
伺服器評量提供兩個調整大小準則選項：

**調整大小準則** | **詳細資料** | **Data**
--- | --- | ---
**以效能為基礎** | 根據所收集的效能資料做出建議的評量 | **VMware VM 評量**：以 CPU 和記憶體使用量資料為基礎的 VM 大小建議。<br/><br/> 磁碟類型建議 (標準 HDD/SSD 或進階受控磁碟) 是以內部部署磁碟的 IOPS 和輸送量為基礎。<br/><br/> **Azure VMware 解決方案 (AVS) 評量**：以 CPU 和記憶體使用量資料為基礎的 AVS 節點建議。
**依內部部署** | 不使用效能資料來提出建議的評量。 | **VMware VM 評量**：VM 大小建議是根據內部部署 VM 大小而提供<br/><br> 建議的磁碟類型是根據您在評量的儲存體類型設定中所選取的內容而定。<br/><br/> **Azure VMware 解決方案 (AVS) 評量**：AVS 節點建議是根據內部部署 VM 大小而提供。

#### <a name="example"></a>範例
例如，如果您的內部部署 VM 有四個核心的20% 使用率，而記憶體 8 GB 的使用率有10%，則 Azure VM 評量如下所示：

- 以**效能為基礎的評**量：
    - 識別以核心 (4 x 0.20 = 0.8) 為基礎的有效核心和記憶體，以及記憶體 (8 GB x 0.10 = 0.8) 使用率。
    - 套用評量屬性中指定的緩和因素 (假設為 1.3 x) ，以取得要用於調整大小的值。 
    - 建議 Azure 中最接近的 VM 大小，可支援 ~ 1.04 核心 (0.8 x 1.3) ，以及 ~ 1.04 GB (0.8 x 1.3) 記憶體。

- **如同內部部署) 評量一樣 (**：
    -  建議有四個核心的 VM;8 GB 的記憶體。


## <a name="best-practices-for-creating-assessments"></a>建立評量的最佳做法

Azure Migrate 設備會持續分析您的內部部署環境，並將中繼資料和效能資料傳送至 Azure。 遵循下列最佳作法來評定使用設備探索到的伺服器：

- 依原樣**建立評定**：當您的機器出現在 Azure Migrate 入口網站中時，您就可以立即建立同型評量。
- **建立以效能為基礎的評**量：設定探索之後，建議您至少等候一天，再執行以效能為基礎的評量：
    - 收集效能資料需要一些時間。 等候至少一天，可確保在執行評量之前有足夠的效能資料點。
    - 當您執行以效能為基礎的評量時，請務必分析您的環境中的評估持續時間。 例如，如果您建立的評量將效能持續時間設定為一周，則在開始探索之後，您需要至少等待一周，以收集所有資料點。 如果您沒有這麼做，評量將不會得到五顆星的評等。
- **重新計算評定**：由於評量是時間點快照集，因此不會自動更新為最新的資料。 若要以最新的資料更新評量，您需要重新計算。

遵循這些最佳作法，以透過將匯入 Azure Migrate 的伺服器進行評量。CSV 檔案：

- 依原樣**建立評定**：當您的機器出現在 Azure Migrate 入口網站中時，您就可以立即建立同型評量。
- **建立以效能為基礎的評**量：這有助於取得更好的成本估計值，特別是當您在內部部署過度布建伺服器容量時。 不過，以效能為基礎的評量的精確度取決於您針對伺服器所指定的效能資料。 
- **重新計算評定**：由於評量是時間點快照集，因此不會自動更新為最新的資料。 若要使用最新匯入的資料來更新評量，您需要重新計算。
 
### <a name="ftt-sizing-parameters-for-avs-assessments"></a>適用于 AVS 評量的 FTT 調整大小參數

AVS 中使用的儲存引擎是 vSAN。 vSAN 儲存原則會定義虛擬機器的儲存體需求。 這些原則可確保 VM 所需的服務層級，因為原則會決定如何將儲存體配置給 VM。 以下是可用的 FTT-Raid 組合： 

**可容許的失敗 (FTT)** | **RAID 組態** | **最低主機需求** | **大小考量**
--- | --- | --- | --- 
1 | RAID-1 (鏡像) | 3 | 100GB 的 VM 會耗用 200GB。
1 | RAID-5 (抹除編碼) | 4 | 100GB 的 VM 會耗用 133.33GB
2 | RAID-1 (鏡像) | 5 | 100GB 的 VM 會耗用 300GB。
2 | RAID-6 (抹除編碼) | 6 | 100GB 的 VM 會耗用 150GB。
3 | RAID-1 (鏡像) | 7 | 100GB 的 VM 會耗用 400GB。


## <a name="best-practices-for-confidence-ratings"></a>信賴評等的最佳作法

當您執行以效能為基礎的評量時，從1顆星 (最低) 到5顆星的信賴評等 (最高) 會獲得評量的獎勵。 有效地使用信賴評等：
- Azure Migrate Server 評定需要 VM CPU/記憶體的使用量資料。
- 針對連接至內部部署 VM 的每個磁片，它都需要讀取/寫入 IOPS/輸送量資料。
- 針對連接至 VM 的每個網路介面卡，它需要網路輸入/輸出資料。

根據所選持續時間可用的資料點百分比，會提供評量的信賴評等，如下表所摘要。

   **資料點可用性** | **信賴評等**
   --- | ---
   0%-20% | 1 顆星
   21%-40% | 2 顆星
   41%-60% | 3 顆星
   61%-80% | 4 顆星
   81%-100% | 5 顆星


## <a name="common-assessment-issues"></a>常見的評定問題

以下說明如何解決一些影響評量的常見環境問題。

###  <a name="out-of-sync-assessments"></a>同步評估

如果您在建立評量之後，在群組中新增或移除機器，您所建立的評量將會標示為「不 **同步**」。再次執行評量 (**重新計算**) 以反映群組變更。

### <a name="outdated-assessments"></a>過期的評量

如果已評估群組中的 Vm 發生內部部署變更，則會將評量標示為 **過期**。 評量可標示為「已過期」，因為以下屬性有一或多項變更：

- 處理器核心數目
- 配置的記憶體
- 開機類型或固件
- 作業系統名稱、版本和架構
- 磁碟數量
- 網路介面卡數目
- 磁片大小變更 (GB 配置) 
- Nic 屬性更新。 範例： Mac 位址變更、新增 IP 位址等。

再次執行評量 (**重新計算**) 以反映變更。

### <a name="low-confidence-rating"></a>低信賴等級

評量可能沒有所有資料點的原因如下：

- 您未針對正在建立評量的持續時間剖析環境。 例如，如果您要建立以 *效能為基礎的評* 量，並將效能持續時間設定為一周，則在開始探索之後，您必須等候至少一周，才能收集所有資料點。 您隨時可以按一下 [ **重新計算** ]，以查看最新的適用信賴評等。 只有當您建立以 *效能為基礎* 的評量時，才適用信賴評等。

- 少數虛擬機器在評量計算期間關閉。 如果有某些 VM 在某段期間內關閉電源，伺服器評量將無法收集這段期間的效能資料。

- 有少數 VM 是在伺服器評量中的探索啟動後建立的。 例如，如果您要建立過去一個月的效能記錄評量，但是少數虛擬機器在一週前才建立在環境中。 在此情況下，將無法取得新的 VM 在這整段期間內的效能資料，且信賴評等將會偏低。

### <a name="migration-tool-guidance-for-avs-assessments"></a>適用于 AVS 評量的遷移工具指導方針

在 Azure VMware 解決方案 (AVS) 評量的 Azure 移轉整備程度報表中，您可以看到下列建議的工具： 
- **VMWARE HCX 或企業**：若為 vmware 機器，vmware 混合式雲端擴充 (HCX) 解決方案是建議的遷移工具，可將內部部署工作負載遷移至 Azure VMware 解決方案 (AVS) 私用雲端。 [深入了解](../azure-vmware/tutorial-deploy-vmware-hcx.md)。
- **未知**：針對透過 CSV 檔案匯入的電腦，預設的移轉工具是未知的。 不過，對於 VMware 機器，建議使用 VMware 混合式雲端擴充功能 (HCX) 解決方案。


## <a name="next-steps"></a>後續步驟

- [瞭解](concepts-assessment-calculation.md) 如何計算評量。
- [瞭解](how-to-modify-assessment.md) 如何自訂評量。
