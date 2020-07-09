---
title: 針對 Azure Migrate 中的評估和相依性視覺效果進行疑難排解
description: 在 Azure Migrate 中取得疑難排解評估和相依性視覺效果的協助。
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: 61afc3ec0f37f5d8b1030818d21b7daabb7fce40
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121668"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>評量/相依性視覺效果疑難排解

本文可協助您使用[Azure Migrate：伺服器評](migrate-services-overview.md#azure-migrate-server-assessment-tool)量，針對評估和相依性視覺效果的問題進行疑難排解。


## <a name="assessment-readiness-issues"></a>評估就緒問題

修正評估就緒問題，如下所示：

**問題** | 修正
--- | ---
不支援的開機類型 | Azure 不支援具有 EFI 開機類型的 Vm。 建議您在執行遷移之前，先將開機類型轉換成 BIOS。 <br/><br/>您可以使用 Azure Migrate 伺服器遷移來處理這類 Vm 的遷移。 在遷移期間，它會將 VM 的開機類型轉換成 BIOS。
有條件支援的 Windows 作業系統 | 作業系統已通過其終止支援的日期，且需要自訂支援合約（CSA）以[在 Azure 中支援](https://aka.ms/WSosstatement)。 在遷移至 Azure 之前，請考慮升級。
不支援的 Windows 作業系統 | Azure 僅支援[選取的 WINDOWS OS 版本](https://aka.ms/WSosstatement)。 在遷移至 Azure 之前，請考慮先升級電腦。
有條件地背書 Linux 作業系統 | Azure 只會背書[選取的 LINUX 作業系統版本](../virtual-machines/linux/endorsed-distros.md)。 在遷移至 Azure 之前，請考慮先升級電腦。
未背書的 Linux 作業系統 | 電腦可能會在 Azure 中啟動，但 Azure 不會提供作業系統支援。 在遷移至 Azure 之前，請考慮升級至已[背書的 Linux 版本](../virtual-machines/linux/endorsed-distros.md)。
作業系統不明 | VM 的作業系統在 vCenter Server 中指定為「其他」。 此行為會封鎖 Azure Migrate 確認 VM 的 Azure 是否就緒。 在您遷移機器之前，請確定 Azure[支援](https://aka.ms/azureoslist)此作業系統。
不支援的位版本 | 具有32位作業系統的 Vm 可能會在 Azure 中開機，但建議您先升級至64位，然後再遷移至 Azure。
需要 Microsoft Visual Studio 訂用帳戶 | 機器執行的是 Windows 用戶端作業系統，只有透過 Visual Studio 訂用帳戶才支援。
找不到所需儲存體效能的 VM | 機器所需的儲存體效能（每秒輸入/輸出作業數 [IOPS] 和輸送量）超過 Azure VM 支援。 在移轉之前，降低機器的儲存體需求。
找不到所需網路效能的 VM | 機器需要的網路效能 (傳入/傳出) 超出 Azure VM 支援。 減少機器的網路需求。
在指定的位置中找不到 VM | 在移轉之前，使用不同的目標位置。
一或多個不適合的磁片 | 連接至 VM 的一或多個磁片不符合 Azure 需求。為<br/><br/> Azure Migrate：伺服器評定目前不支援 Ultra SSD 磁片，並會根據 premium 受控磁片的磁片限制（32 TB）來評估磁片。<br/><br/> 針對連接至 VM 的每個磁片，請確定磁片的大小為 < 64 TB （Ultra SSD 磁片支援）。<br/><br/> 如果不是，請在遷移至 Azure 之前先減少磁片大小，或在 Azure 中使用多個磁片，並將它們等量放在[一起](../virtual-machines/windows/premium-storage-performance.md#disk-striping)，以取得更高的儲存空間限制。 請確定 Azure[受管理的虛擬機器磁片](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)支援每個磁片所需的效能（IOPS 和輸送量）。
一個或多個不適用的網路介面卡。 | 在移轉之前，從機器移除未使用的網路介面卡。
磁碟計數超過限制 | 移轉之前從機器移除未使用的磁碟。
磁碟大小超過限制 | Azure Migrate：伺服器評定目前不支援 Ultra SSD 磁片，並會根據 premium 磁片限制（32 TB）來評估磁片。<br/><br/> 不過，Azure 支援高達 64 TB 大小的磁片（Ultra SSD 磁片支援）。 在遷移之前，將磁片壓縮成小於 64 TB，或在 Azure 中使用多個磁片，並將它們等量放在[一起](../virtual-machines/windows/premium-storage-performance.md#disk-striping)，以取得更高的儲存體限制
指定的位置沒有磁碟可用 | 在移轉之前，請確定磁碟已在目標位置。
沒有磁碟可當作指定的備援 | 磁碟應該使用評估設定 (預設為 LRS) 中定義的備援儲存體類型。
因為發生內部錯誤，所以無法判斷磁片適用性 | 嘗試建立群組的新評估。
找不到具備所需核心和記憶體的虛擬機器 | Azure 找不到適合的 VM 類型。 在移轉之前，請減少內部部署電腦的記憶體和核心數目。
因為發生內部錯誤，所以無法判斷 VM 適用性 | 嘗試建立群組的新評估。
因為發生內部錯誤，所以無法判斷一或多個磁片的適用性 | 嘗試建立群組的新評估。
因為發生內部錯誤，所以無法判斷一或多個網路介面卡的適用性 | 嘗試建立群組的新評估。
找不到供應專案貨幣保留實例的 VM 大小 | 標示為「不適合」的機器，因為找不到所選 RI、供應專案和貨幣組合的 VM 大小。 編輯評量屬性以選擇有效的組合，並重新計算評量。 
有條件地準備好的網際網路通訊協定 | 僅適用于 Azure VMware 解決方案（AVS）評量。 AVS 不支援 IPv6 網際網路位址因素。如果您使用 IPv6 偵測到您的電腦，請洽詢 AVS 小組以取得補救指導方針。

## <a name="suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>以匯入為基礎的 AVS 評估中的建議遷移工具標示為不明

針對透過 CSV 檔案匯入的電腦，和 AVS 評估中的預設遷移工具是未知的。 不過，對於 VMware 機器，建議使用 VMWare 混合式雲端擴充功能（HCX）解決方案。 [深入了解](../azure-vmware/hybrid-cloud-extension-installation.md)。

## <a name="linux-vms-are-conditionally-ready-in-an-azure-vm-assessment"></a>在 Azure VM 評估中，Linux Vm 已「有條件地準備就緒」

在 VMware 和 Hyper-v Vm 的情況下，伺服器評量會將 Linux Vm 標示為「有條件地準備就緒」，因為伺服器評估有已知的差距。 

- 此缺口會使其無法偵測內部部署 Vm 上所安裝之 Linux OS 的次要版本。
- 例如，針對 RHEL 6.10，目前的伺服器評估只會偵測 RHEL 6 做為作業系統版本。 這是因為 vCenter Server ar，Hyper-v 主機不會提供 Linux VM 作業系統的核心版本。
-  因為 Azure 只背書特定版本的 Linux，所以 Linux Vm 目前在伺服器評估中會標示為有條件地準備就緒。
- 您可以藉由查看[Azure Linux 支援](https://aka.ms/migrate/selfhost/azureendorseddistros)，判斷在內部部署 VM 上執行的 Linux 作業系統是否已背書 azure。
-  確認背書的散發套件之後，您可以忽略此警告。

在 VMware Vm 上啟用[應用程式探索](./how-to-discover-applications.md)，即可解決此間隙。 伺服器評估會使用所提供的來賓認證，從 VM 偵測到的作業系統。 在 Windows 和 Linux Vm 的情況下，此作業系統資料會識別正確的作業系統資訊。


## <a name="azure-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>在 Azure VM 評估中大於內部部署的 azure Sku

根據評量類型，Azure Migrate Server 評估可能會建議具有更多核心和記憶體的 Azure VM Sku，而不是目前的內部部署配置：


- VM SKU 建議取決於評量屬性。
- 這會受到您在伺服器評估中執行的評量類型所影響：以*效能為基礎*，或*作為內部部署*。
- 針對以效能為基礎的評量，伺服器評估會考慮內部部署 Vm （CPU、記憶體、磁片和網路使用率）的使用量資料，以判斷內部部署 Vm 的正確目標 VM SKU。 在決定有效的使用率時，也會增加緩和因素。
- 若為內部部署調整大小，則不會考慮效能資料，而且建議您根據內部部署配置來使用目標 SKU。

為了說明這會如何影響建議，讓我們來看一個範例：

我們有一個內部部署 VM 具有四個核心和 8 GB 的記憶體，具有 50% CPU 使用率和50% 的記憶體使用率，以及指定的緩和因數1.3。

-  如果評估為**內部部署**，則建議使用具有四個核心和 8 GB 記憶體的 AZURE VM SKU。
- 如果評量是以效能為基礎，根據有效的 CPU 和記憶體使用率（4核心的 50% * 1.3 = 2.6 核心和50% 的 8 GB 記憶體 * 1.3 = 5.3-GB 記憶體），建議使用四個核心的最低 VM SKU （最接近支援的核心計數）和 8 GB 的記憶體（最接近支援的記憶體大小）。
- [深入瞭解](concepts-assessment-calculation.md#types-of-assessments)評量大小。

## <a name="azure-disk-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Azure VM 評估中大於內部部署的 azure 磁片 Sku

Azure Migrate Server 評估可能會根據評量的類型，建議較大的磁片。
- 伺服器評估中的磁片大小取決於兩個評估屬性：調整大小準則和儲存體類型。
- 如果調整大小準則是以**效能為基礎**，且儲存體類型設定為 [**自動**]，則識別目標磁片類型（標準 HDD、標準 SSD 或 Premium）時，會考慮磁片的 IOPS 和輸送量值。 然後建議使用磁片類型的磁片 SKU，而建議會考慮內部部署磁片的大小需求。
- 如果調整大小準則是以**效能為基礎**，且儲存體類型為**Premium**，則根據內部部署磁片的 IOPS、輸送量和大小需求，建議 Azure 中的 Premium 磁片 SKU。 當調整大小準則是**作為內部部署**，且儲存類型為**標準 HDD**、**標準 SSD**或**Premium**時，會使用相同的邏輯來執行磁片大小調整。

例如，如果您的內部部署磁片具有 32 GB 的記憶體，但是磁片的匯總讀取和寫入 IOPS 是 800 IOPS，伺服器評估會建議 premium 磁片（因為 IOPS 需求較高），然後建議可支援所需 IOPS 和大小的磁片 SKU。 在此範例中最接近的相符項目為 P15 (256 GB、1100 IOPS)。 即使內部部署磁片所需的大小為 32 GB，伺服器評估還是會建議較大的磁片，因為內部部署磁片的高 IOPS 需求。

## <a name="utilized-corememory-percentage-missing"></a>已使用的核心/記憶體百分比遺失

當 Azure Migrate 設備無法收集相關內部部署 Vm 的效能資料時，伺服器評估會回報 "PercentageOfCoresUtilizedMissing" 或 "PercentageOfMemoryUtilizedMissing"。

- 如果在評估期間關閉 Vm，就會發生這種情況。 應用裝置關閉時，無法收集 VM 的效能資料。
- 如果只有記憶體計數器遺失，而且您正嘗試評估 Hyper-v Vm，請檢查您是否已在這些 Vm 上啟用動態記憶體。 只有 Hyper-v Vm 的已知問題，在此情況下，Azure Migrate 設備無法收集未啟用動態記憶體之 Vm 的記憶體使用量資料。
- 如果遺失任何效能計數器，Azure Migrate Server 評估會降回已配置的核心和記憶體，並建議對應的 VM 大小。
- 如果遺失所有效能計數器，請確定已符合評估的埠存取需求。 深入瞭解[VMware](./migrate-support-matrix-vmware.md#port-access-requirements)、 [hyper-v](./migrate-support-matrix-hyper-v.md#port-access)和[實體](./migrate-support-matrix-physical.md#port-access)伺服器評量的埠存取需求。

## <a name="is-the-operating-system-license-included-in-an-azure-vm-assessment"></a>是否包含在 Azure VM 評估中的作業系統授權？

Azure Migrate Server 評估目前只考慮 Windows 機器的作業系統授權成本。 目前未考慮到 Linux 機器的授權成本。

## <a name="how-does-performance-based-sizing-work-in-an-azure-vm-assessment"></a>以效能為基礎的調整大小在 Azure VM 評估中的運作方式為何？

伺服器評量會持續收集內部部署機器的效能資料，並用這些資料來建議 Azure 中的 VM SKU 和磁碟 SKU。 [瞭解如何](concepts-assessment-calculation.md#calculate-sizing-performance-based)收集以效能為基礎的資料。

## <a name="why-is-my-assessment-showing-a-warning-that-it-was-created-with-an-invalid-combination-of-reserved-instances-vm-uptime-and-discount-"></a>為什麼我的評量顯示使用不正確保留實例、VM 執行時間和折扣（%）組合所建立的警告？
當您選取 [保留實例] 時，[折扣（%）]和「VM 執行時間」屬性不適用。 當您使用不正確這些屬性組合來建立評估時，[編輯] 和 [重新計算] 按鈕會停用。 請建立新的評量。 [深入了解](https://go.microsoft.com/fwlink/?linkid=2131554)。

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>我在實體伺服器上看不到某些網路介面卡的效能資料

如果實體伺服器已啟用 Hyper-v 虛擬化，就會發生這種情況。 在這些伺服器上，由於產品的差距，Azure Migrate 目前會探索實體和虛擬網路介面卡。 只會在探索到的虛擬網路介面卡上捕獲網路輸送量。

## <a name="recommended-azure-vm-sku-for-my-physical-server-is-oversized"></a>適用于我的實體伺服器的建議 Azure VM SKU 已過大

如果實體伺服器已啟用 Hyper-v 虛擬化，就會發生這種情況。 在這些伺服器上，Azure Migrate 目前會探索實體和虛擬網路介面卡。 因此，也就是「否」。 探索到的網路介面卡高於實際數目。 當伺服器評估挑選的 Azure VM 可支援所需的網路介面卡數目時，這可能會導致 VM 過大。 [深入瞭解](./concepts-assessment-calculation.md#calculating-sizing)「否」的影響。 調整大小時的網路介面卡。 這是即將解決的產品缺口。

## <a name="readiness-category-not-ready-for-my-physical-server"></a>我的實體伺服器的就緒類別「未就緒」

如果實體伺服器已啟用 Hyper-v 虛擬化，則 [就緒] 類別可能會錯誤地標示為「未就緒」。 在這些伺服器上，由於產品的差距，Azure Migrate 目前會探索實體和虛擬介面卡。 因此，也就是「否」。 探索到的網路介面卡高於實際數目。 在內部部署和以效能為基礎的評量中，伺服器評估會挑選可支援所需的網路介面卡數目的 Azure VM。 如果探索到的網路介面卡數目高於32，則上限為 [否]。 在 Azure Vm 上支援的 Nic 數目，電腦會標示為「未就緒」。  [深入瞭解](./concepts-assessment-calculation.md#calculating-sizing)「否」的影響。 調整大小的 Nic。


## <a name="number-of-discovered-nics-higher-than-actual-for-physical-servers"></a>已探索到的 Nic 數高於實體伺服器的實際數目

如果實體伺服器已啟用 Hyper-v 虛擬化，就會發生這種情況。 在這些伺服器上，Azure Migrate 目前會探索實體和虛擬介面卡。 因此，也就是「否」。 探索到的 Nic 高於實際數目。


## <a name="low-confidence-rating-on-physical-server-assessments"></a>實體伺服器評量的低信賴等級
評等是根據計算評量所需的資料點可用性來指派。 如果實體伺服器已啟用 Hyper-v 虛擬化，則會有已知的產品差距，因為可能會不正確地將較低的信賴評等指派給實體伺服器評量。 在這些伺服器上，Azure Migrate 目前會探索實體和虛擬介面卡。 網路輸送量會在探索到的虛擬網路介面卡上捕捉，而不是在實體網路介面卡上。 由於實體網路介面卡上沒有資料點，因此信賴評等可能會受到影響，因而產生較低的評等。 這是即將解決的產品缺口。

## <a name="dependency-visualization-in-azure-government"></a>Azure Government 中的相依性視覺效果

Azure Migrate 取決於相依性視覺效果功能的服務對應。 因為服務對應目前無法在 Azure Government 中使用，所以 Azure Government 不提供這項功能。

## <a name="dependencies-dont-show-after-agent-install"></a>代理程式安裝後不會顯示相依性

在內部部署 Vm 上安裝相依性視覺化代理程式之後，Azure Migrate 通常需要15-30 分鐘的時間，才能在入口網站中顯示相依性。 如果您已等候超過30分鐘，請確定 Microsoft Monitoring Agent （MMA）可以連線到 Log Analytics 工作區。

若為 Windows VM：
1. 在 [控制台] 中，啟動 MMA。
2. 在 [ **Microsoft Monitoring Agent 屬性**] [  >  **Azure Log Analytics （OMS）**] 中，確認工作區的 [**狀態**] 為綠色。
3. 如果狀態不是綠色，請嘗試移除工作區，然後再次將它新增至 MMA。

    ![MMA 狀態](./media/troubleshoot-assessment/mma-properties.png)

針對 Linux Vm，請確定 MMA 和 dependency agent 的安裝命令已成功。

## <a name="supported-operating-systems"></a>支援的作業系統

- **MMS 代理程式**：審查支援的[Windows](../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)和[Linux](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)作業系統。
- **Dependency agent**：支援的[Windows 和 Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)作業系統。

## <a name="visualize-dependencies-for--hour"></a>將 > 小時的相依性視覺化

透過無代理程式相依性分析，您可以將相依性視覺化，或在對應中將其匯出，持續時間最長為30天。

使用代理程式相依性分析時，雖然 Azure Migrate 允許您回到上個月的特定日期，但您可將相依性視覺化的最大持續時間為一小時。 例如，您可以使用相依性對應中的 [持續時間] 功能來查看昨天的相依性，但只能在一小時的期間內進行查看。 不過，您可以使用 Azure 監視器記錄，在較長的持續期間內查詢相依性[資料](./how-to-create-group-machine-dependencies.md)。

## <a name="visualized-dependencies-for--10-machines"></a>視覺化 > 10 部電腦的相依性

在 Azure Migrate Server 評估中，使用代理程式相依性分析時，您可以將最多10個 Vm 的群組相依性[視覺化](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping)。 針對較大的群組，建議您將 Vm 分割成較小的群組，以將相依性視覺化。


## <a name="machines-show-install-agent"></a>機器顯示「安裝代理程式」

將已啟用相依性視覺效果的機器遷移至 Azure 之後，電腦可能會因為下列行為而顯示「安裝代理程式」動作，而不是「查看相依性」：


- 遷移至 Azure 之後，會關閉內部部署機器，而對等 Vm 會在 Azure 中啟動。 這些機器會取得不同的 MAC 位址。
- 根據您是否已保留內部部署 IP 位址，機器可能也會有不同的 IP 位址。
- 如果 MAC 和 IP 位址都與內部部署不同，Azure Migrate 不會將內部部署機器與任何服務對應相依性資料產生關聯。 在此情況下，它會顯示安裝代理程式的選項，而不是用來查看相依性。
- 將測試遷移至 Azure 之後，內部部署機器仍會如預期般開啟。 在 Azure 中啟動的對等電腦會取得不同的 MAC 位址，而且可能會取得不同的 IP 位址。 除非您封鎖來自這些電腦的傳出 Azure 監視器記錄流量，否則 Azure Migrate 不會將內部部署機器與任何服務對應相依性資料產生關聯，因此會顯示安裝代理程式的選項，而不是查看相依性。

## <a name="dependencies-export-csv-shows-unknown-process"></a>相依性匯出 CSV 顯示「未知的進程」
在無代理程式相依性分析中，會以最大的方式來捕捉進程名稱。 在某些情況下，雖然會捕捉來源和目的地伺服器名稱和目的地埠，但在相依性的兩端判斷進程名稱並不可行。 在這種情況下，進程會標示為「未知的進程」。


## <a name="capture-network-traffic"></a>捕捉網路流量

收集網路流量記錄，如下所示：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按 F12 啟動開發人員工具。 如有需要，請清除 [**清除導覽上的專案**] 設定。
3. 選取 [**網路**] 索引標籤，然後開始捕獲網路流量：
   - 在 Chrome 中，選取 [保留記錄]****。 記錄應該會自動啟動。 紅色圓圈表示正在捕捉流量。 如果未出現紅色圓圈，請選取要啟動的黑色圓圈。
   - 在 Microsoft Edge 和 Internet Explorer 中，記錄應該會自動啟動。 如果不是，請選取綠色的 [播放] 按鈕。
4. 嘗試重現錯誤。
5. 您在記錄時發生錯誤之後，請停止錄製，並儲存一份記錄的活動：
   - 在 Chrome 中，以滑鼠右鍵按一下並選取 [**與內容一起另存為 HAR**]。 此動作會將記錄壓縮並匯出為 har 檔案。
   - 在 Microsoft Edge 或 Internet Explorer 中，選取 [**匯出已捕捉的流量**] 選項。 此動作會壓縮並匯出記錄檔。
6. 選取 [**主控台**] 索引標籤，以檢查是否有任何警告或錯誤。 若要儲存主控台記錄：
   - 在 Chrome 中，以滑鼠右鍵按一下主控台記錄的任何位置。 選取 [**另存**新檔]，匯出並壓縮記錄檔。
   - 在 Microsoft Edge 或 Internet Explorer 中，以滑鼠右鍵按一下錯誤，然後選取 [**全部複製**]。
7. 關閉 Developer Tools。


## <a name="where-is-the-operating-system-data-in-my-assessment-discovered-from"></a>我的評估會從哪裡探索作業系統資料？

- 針對 VMware Vm，根據預設，它是 vCenter 所提供的作業系統資料。 
   - 針對 VMware linux Vm，如果已啟用應用程式探索，則會從來賓 VM 提取 OS 詳細資料。 若要檢查評量中的 OS 詳細資料，請移至 [探索到的伺服器]，並將滑鼠停留在 [作業系統] 資料行中的值。 在快顯的文字中，您可以看到您看到的作業系統資料是從 vCenter server 還是使用 VM 認證從來賓 VM 收集而來。 
   - 就 Windows Vm 而言，系統一律會從 vCenter Server 提取作業系統詳細資料。
- 針對 Hyper-v Vm，系統會從 Hyper-v 主機收集作業系統資料
- 若為實體伺服器，則會從伺服器提取它。

## <a name="next-steps"></a>後續步驟

[建立](how-to-create-assessment.md)或[自訂](how-to-modify-assessment.md)評量。
