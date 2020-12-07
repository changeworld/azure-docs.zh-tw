---
title: 針對 Azure Migrate 中的評定和相依性視覺效果進行疑難排解
description: 取得 Azure Migrate 中評定和相依性視覺效果的協助。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: cefcd4ce287eecfe2c764d88d5d2233cc8ac0a5c
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753440"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>評量/相依性視覺效果疑難排解

本文可協助您使用 [Azure Migrate：伺服器評](migrate-services-overview.md#azure-migrate-server-assessment-tool)量，針對評定和相依性視覺效果的問題進行疑難排解。


## <a name="assessment-readiness-issues"></a>評量準備問題

修正評估就緒問題，如下所示：

**問題** | 修正
--- | ---
不支援的開機類型 | Azure 不支援具有 EFI 開機類型的 Vm。 建議您在執行遷移之前，先將開機類型轉換成 BIOS。 <br/><br/>您可以使用 Azure Migrate Server 遷移來處理這類 Vm 的遷移。 它會在遷移期間將 VM 的開機類型轉換成 BIOS。
有條件地支援的 Windows 作業系統 | 作業系統已通過終止支援的日期，且需要自訂支援合約 (CSA) 以 [支援 Azure](/troubleshoot/azure/virtual-machines/server-software-support)。 在遷移至 Azure 之前，請考慮進行升級。 請[參閱]()[準備執行 Windows Server 2003 的機器](prepare-windows-server-2003-migration.md)以遷移至 Azure 的相關資訊。
不支援的 Windows 作業系統 | Azure 僅支援 [選取的 WINDOWS 作業系統版本](/troubleshoot/azure/virtual-machines/server-software-support)。 在遷移至 Azure 之前，請考慮先升級機器。
有條件地背書 Linux 作業系統 | Azure 只會背書 [選取的 LINUX OS 版本](../virtual-machines/linux/endorsed-distros.md)。 在遷移至 Azure 之前，請考慮先升級機器。 另請參閱 [這裡](#linux-vms-are-conditionally-ready-in-an-azure-vm-assessment) 以取得詳細資料。
未背書的 Linux 作業系統 | 電腦可能會在 Azure 中啟動，但 Azure 不會提供作業系統支援。 在遷移至 Azure 之前，請考慮升級至已 [背書的 Linux 版本](../virtual-machines/linux/endorsed-distros.md) 。
作業系統不明 | 在 vCenter Server 中，已將 VM 的作業系統指定為「其他」。 此行為會封鎖 Azure Migrate 確認 VM 的 Azure 是否就緒。 在您遷移電腦之前，請先確定 Azure [支援](./migrate-support-matrix-vmware-migration.md#azure-vm-requirements) 作業系統。
不支援的位版本 | 具有32位作業系統的 Vm 可能會在 Azure 中開機，但建議您在遷移至 Azure 之前，先升級至64位。
需要 Microsoft Visual Studio 訂用帳戶 | 電腦正在執行 Windows 用戶端作業系統，僅透過 Visual Studio 訂用帳戶支援。
找不到所需儲存體效能的 VM | 儲存體效能 (機器所需的每秒輸入/輸出作業 [IOPS] 和輸送量) 超過 Azure VM 支援。 在移轉之前，降低機器的儲存體需求。
找不到所需網路效能的 VM | 機器需要的網路效能 (傳入/傳出) 超出 Azure VM 支援。 減少機器的網路需求。
在指定的位置找不到 VM | 在移轉之前，使用不同的目標位置。
一或多個不適合的磁片 | 一或多個連接至 VM 的磁片不符合 Azure 需求。的<br/><br/> Azure Migrate：伺服器評量目前不支援 Ultra SSD 磁片，並根據高階受控磁片的磁片限制來評估磁片 (32 TB) 。<br/><br/> 針對連結至 VM 的每個磁片，請確定 <) 的 Ultra SSD 磁片 (的磁片大小為 64 TB。<br/><br/> 如果不是，請在遷移至 Azure 之前先減少磁片大小，或在 Azure 中使用多個磁片，並將 [它們集中在一起](../virtual-machines/premium-storage-performance.md#disk-striping) ，以取得更高的儲存體限制。 請確定 Azure [受管理的虛擬機器磁片](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)支援每個磁片所需的效能 (IOPS 和輸送量) 。
一個或多個不適用的網路介面卡。 | 在移轉之前，從機器移除未使用的網路介面卡。
磁碟計數超過限制 | 移轉之前從機器移除未使用的磁碟。
磁碟大小超過限制 | Azure Migrate：伺服器評量目前不支援 Ultra SSD 磁片，並根據高階磁片限制來評估磁片 (32 TB) 。<br/><br/> 不過，Azure 支援的磁片具有高達 64-TB 的大小 (Ultra SSD 磁片) 支援。 在遷移前將磁片壓縮成小於 64 TB，或在 Azure 中使用多個磁片，並將 [它們集中在一起](../virtual-machines/premium-storage-performance.md#disk-striping) ，以取得更高的儲存體限制。
指定的位置沒有磁碟可用 | 在移轉之前，請確定磁碟已在目標位置。
沒有磁碟可當作指定的備援 | 磁碟應該使用評估設定 (預設為 LRS) 中定義的備援儲存體類型。
因為發生內部錯誤，所以無法判斷磁片適用性 | 嘗試建立群組的新評估。
找不到具備所需核心和記憶體的虛擬機器 | Azure 找不到適合的 VM 類型。 在移轉之前，請減少內部部署電腦的記憶體和核心數目。
因為發生內部錯誤，所以無法判斷 VM 適用性 | 嘗試建立群組的新評估。
因為發生內部錯誤，所以無法判斷一或多個磁片的適用性 | 嘗試建立群組的新評估。
因為發生內部錯誤，所以無法判斷一或多個網路介面卡的適用性 | 嘗試建立群組的新評估。
找不到供應專案貨幣保留實例的 VM 大小 | 因為找不到所選 RI、供應專案和貨幣組合的 VM 大小，所以電腦標示為 [不適用]。 編輯評量屬性以選擇有效的組合，並重新計算評量。 
有條件地準備好網際網路通訊協定 | 僅適用于 Azure VMware Solution (AVS) 評量。 AVS 不支援 IPv6 網際網路位址因素。 如果您的電腦是使用 IPv6 偵測到，請洽詢 AVS 小組以取得補救指導方針。

## <a name="suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>以匯入為基礎的 AVS 評定中的建議遷移工具標示為未知

針對透過 CSV 檔案匯入的電腦，和 AVS 評量中的預設遷移工具不明。 不過，對於 VMware 機器，建議使用 VMware 混合式雲端擴充功能 (HCX) 解決方案。 [深入了解](../azure-vmware/tutorial-deploy-vmware-hcx.md)。

## <a name="linux-vms-are-conditionally-ready-in-an-azure-vm-assessment"></a>Azure VM 評量中的 Linux Vm 「有條件地就緒」

在 VMware 和 Hyper-v Vm 的案例中，伺服器評量會將 Linux Vm 標示為「有條件地就緒」，因為伺服器評量中有已知的間隔。 

- 這項缺口可防止它偵測內部部署 Vm 上安裝的 Linux OS 次要版本。
- 例如，在 RHEL 6.10 中，伺服器評量目前只會偵測到 RHEL 6 作為作業系統版本。 這是因為 vCenter Server ar，Hyper-v 主機未提供 Linux VM 作業系統的核心版本。
-  因為 Azure 只背書特定版本的 Linux，所以 Linux Vm 目前在伺服器評量中標示為有條件地備妥。
- 您可以藉由查看 [Azure Linux 支援](../virtual-machines/linux/endorsed-distros.md)，來判斷在內部部署 VM 上執行的 Linux OS 是否已在 azure 中背書。
-  確認背書發行版本之後，您可以忽略此警告。

在 VMware Vm 上啟用 [應用程式探索](./how-to-discover-applications.md) 可解決此缺口。 伺服器評估會使用所提供的來賓認證，從 VM 偵測到的作業系統。 在 Windows 和 Linux Vm 的情況下，此作業系統資料會識別正確的作業系統資訊。

## <a name="operating-system-version-not-available"></a>作業系統版本無法使用

對於實體伺服器，您應該可以使用作業系統次要版本資訊。 如果無法使用，請聯絡 Microsoft 支援服務。 針對 VMware 機器，伺服器評量會使用 vCenter Server 中為 VM 指定的作業系統資訊。 不過，vCenter Server 不會提供作業系統的次要版本。 若要探索次要版本，您必須設定 [應用程式探索](./how-to-discover-applications.md)。 針對 Hyper-v Vm，不支援作業系統次要版本探索。 

## <a name="azure-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Azure VM 評量中大於內部部署的 azure Sku

Azure Migrate 伺服器評量可能會建議 Azure VM Sku 的核心和記憶體比目前內部部署配置更多（根據評量類型而定）：

- VM SKU 建議取決於評量屬性。
- 這會受到您在伺服器評量中執行的評量類型所影響：以 *效能為基礎* 或 *作為內部部署*。
- 針對以效能為基礎的評量，伺服器評量會考慮內部部署 Vm (CPU、記憶體、磁片和網路使用量) 的使用量資料，以判斷適用于內部部署 Vm 的正確目標 VM SKU。 在判定有效的使用率時，也會增加緩和因數。
- 若為內部部署調整大小，則不會考慮效能資料，而是根據內部部署配置建議目標 SKU。

為了顯示這對建議有何影響，讓我們來看一個範例：

我們的內部部署 VM 具有四個核心和 8 GB 的記憶體，具有50% 的 CPU 使用率和50% 的記憶體使用量，以及指定的緩和因數1.3。

-  如果評量是 **內部部署**，則建議使用具有四個核心和 8 GB 記憶體的 AZURE VM SKU。
- 如果評量以效能為基礎，則根據有效的 CPU 和記憶體使用量 (50% 的4個核心 * 1.3 = 2.6 核心和50% 的 8 GB 記憶體 * 1.3 = 5.3-GB 記憶體) ，四個核心的最便宜 VM SKU (最接近支援的核心計數) 和 8 GB 的記憶體 (建議的最接近支援記憶體大小) 。
- [深入瞭解](concepts-assessment-calculation.md#types-of-assessments) 評量調整大小。

## <a name="why-is-the-recommended-azure-disk-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>為什麼建議的 Azure 磁片 Sku 大於 Azure VM 評量中的內部部署？

Azure Migrate Server 評定可能會根據評量類型建議較大的磁片。
- 伺服器評量中的磁片大小調整取決於兩個評估屬性：調整大小準則和儲存體類型。
- 如果調整大小準則是以 **效能為基礎**，而且儲存體類型設定為 [ **自動**]，則在識別目標磁片類型 (標準 HDD、標準 SSD 或 Premium) 時，會考慮磁片的 IOPS 和輸送量值。 然後建議磁片類型的磁片 SKU，建議您考慮內部部署磁片的大小需求。
- 如果調整大小準則是以 **效能為基礎**，而且儲存體類型是 **Premium**，則建議使用 Azure 中的 Premium 磁片 SKU，根據內部部署磁片的 IOPS、輸送量和大小需求。 當調整大小準則為內部 **部署** ，且儲存體類型為 **標準 HDD**、 **標準 SSD** 或 **Premium** 時，會使用相同的邏輯來執行磁片調整大小。

例如，如果您的內部部署磁片具有 32 GB 的記憶體，但磁片的匯總讀取和寫入 IOPS 是 800 IOPS，伺服器評量會建議 premium 磁片 (因為) 的 IOPS 需求較高，然後建議可支援所需 IOPS 和大小的磁片 SKU。 在此範例中最接近的相符項目為 P15 (256 GB、1100 IOPS)。 即使內部部署磁片所需的大小是 32 GB，伺服器評量仍建議使用較大的磁片，因為內部部署磁片的高 IOPS 需求。

## <a name="why-is-performance-data-missing-for-someall-vms-in-my-assessment-report"></a>為什麼我的評量報告中遺失某些/所有 VM 的效能資料？

進行「以效能為基礎的」評量時，當 Azure Migrate 設備無法收集內部部署 VM 的效能資料時，評量報告匯出會顯示 'PercentageOfCoresUtilizedMissing' 或 'PercentageOfMemoryUtilizedMissing'。 請檢查：

- VM 在您建立評量的持續時間內是否開啟電源
- 如果只有記憶體計數器遺失，而且您想要評估 Hyper-V VM，那麼請檢查您是否已在這些 VM 上啟用動態記憶體。 目前有已知問題導致 Azure Migrate 設備無法對此類 VM 收集記憶體使用量。
- 如果所有的效能計數器都遺失，請確定已符合評估的埠存取需求。 深入瞭解 [VMware](./migrate-support-matrix-vmware.md#port-access-requirements)、 [hyper-v](./migrate-support-matrix-hyper-v.md#port-access) 和 [實體](./migrate-support-matrix-physical.md#port-access) 伺服器評量的埠存取需求。
注意：如果遺漏任何效能計數器，則 Azure Migrate：伺服器評量會回復為已配置的內部部署核心/記憶體，並據此建議 VM 大小。

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>為何我的評量信賴評等偏低？

系統會根據計算評量所需的[可用資料點](./concepts-assessment-calculation.md#ratings)百分比，為「以效能為基礎的」評量計算信賴評等。 以下是評量會獲得低信賴評等的原因：

- 您未針對正在建立評量的持續時間剖析環境。 例如，如果您要建立將效能持續時間設定為一週的評量，您需要至少等待一週後再開始探索，才能收集到所有資料點。 如果您無法等待該持續時間，請將效能持續時間變更為較短的期間，並「重新計算」評量。
 
- 伺服器評量在評估期間內無法收集部分或所有 VM 的效能資料。 請檢查 VM 在評估期間內是否開啟電源，並允許連接埠 443 上的輸出連線。 針對 Hyper-V VM，如果啟用了動態記憶體，記憶體計數器將會遺失，因而導致信賴評等偏低。 請「重新計算」評量，以反映信賴評等的最新變更。 

- 有少數 VM 是在伺服器評量中的探索啟動後建立的。 例如，如果您要建立過去一個月的效能記錄評量，但是少數虛擬機器在一週前才建立在環境中。 在此情況下，將無法取得新的 VM 在這整段期間內的效能資料，且信賴評等將會偏低。

[深入了解](./concepts-assessment-calculation.md#confidence-ratings-performance-based)信賴評等。

## <a name="is-the-operating-system-license-included-in-an-azure-vm-assessment"></a>Azure VM 評量中是否包含作業系統授權？

Azure Migrate 伺服器評量目前只會考慮 Windows 機器的作業系統授權成本。 目前不考慮 Linux 機器的授權成本。

## <a name="how-does-performance-based-sizing-work-in-an-azure-vm-assessment"></a>以效能為基礎的大小調整在 Azure VM 評量中的運作方式為何？

伺服器評量會持續收集內部部署機器的效能資料，並用這些資料來建議 Azure 中的 VM SKU 和磁碟 SKU。 [瞭解如何](concepts-assessment-calculation.md#calculate-sizing-performance-based) 收集以效能為基礎的資料。

## <a name="why-is-my-assessment-showing-a-warning-that-it-was-created-with-an-invalid-combination-of-reserved-instances-vm-uptime-and-discount-"></a>為什麼我的評量會顯示一則警告，指出它是使用不正確保留實例組合來建立、VM 執行時間和折扣 (% ) ？
當您選取 [保留實例] 時，[折扣 (% ) ] 和 [VM 執行時間] 屬性都不適用。 當您的評量是以不正確這些屬性組合來建立時，就會停用 [編輯] 和 [重新計算] 按鈕。 請建立新的評量。 [深入了解](./concepts-assessment-calculation.md#whats-an-assessment)。

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>我看不到實體伺服器上某些網路介面卡的效能資料

如果實體伺服器已啟用 Hyper-v 虛擬化，就會發生這種情況。 在這些伺服器上，由於產品間距的緣故，Azure Migrate 目前會探索實體和虛擬網路介面卡。 只有在探索到的虛擬網路介面卡上，才會捕獲網路輸送量。

## <a name="recommended-azure-vm-sku-for-my-physical-server-is-oversized"></a>適用于我的實體伺服器的建議 Azure VM SKU 已過超大

如果實體伺服器已啟用 Hyper-v 虛擬化，就會發生這種情況。 在這些伺服器上，Azure Migrate 目前會探索實體和虛擬網路介面卡。 因此， 探索到的網路介面卡高於實際的網路介面卡。 由於伺服器評量挑選的 Azure VM 可支援所需的網路介面卡數目，因此可能會導致 VM 過大。 [深入瞭解](./concepts-assessment-calculation.md#calculating-sizing) 「否」的影響。 大小調整的網路介面卡。 這是即將解決的產品缺口。

## <a name="readiness-category-not-ready-for-my-physical-server"></a>我的實體伺服器的就緒類別「尚未就緒」

如果已啟用 Hyper-v 虛擬化的實體伺服器，就緒類別可能會錯誤地標示為「未就緒」。 在這些伺服器上，由於產品間距的緣故，Azure Migrate 目前會探索實體和虛擬配接器。 因此， 探索到的網路介面卡高於實際的網路介面卡。 在內部部署和以效能為基礎的評量中，伺服器評量會挑選可支援所需網路介面卡數目的 Azure VM。 如果探索到的網路介面卡數目大於32，則最大值為 [否]。 在 Azure Vm 上支援的 Nic，電腦會標示為「未就緒」。  [深入瞭解](./concepts-assessment-calculation.md#calculating-sizing) 「否」的影響。 進行調整大小的 Nic。


## <a name="number-of-discovered-nics-higher-than-actual-for-physical-servers"></a>探索到的 Nic 數目高於實體伺服器的實際數目

如果實體伺服器已啟用 Hyper-v 虛擬化，就會發生這種情況。 在這些伺服器上，Azure Migrate 目前會探索實體和虛擬的介面卡。 因此， 探索到的 Nic 高於實際的數目。

## <a name="dependency-visualization-in-azure-government"></a>Azure Government 中的相依性視覺效果

Azure Government 不支援以代理程式為基礎的相依性分析。 請使用無代理程式相依性分析。


## <a name="dependencies-dont-show-after-agent-install"></a>代理程式安裝後不會顯示相依性

在內部部署 Vm 上安裝相依性視覺效果代理程式之後，Azure Migrate 通常需要15-30 分鐘的時間，才會在入口網站中顯示相依性。 如果您等候超過30分鐘，請確定 Microsoft Monitoring Agent (MMA) 可以連線到 Log Analytics 工作區。

若為 Windows VM：
1. 在主控台中，啟動 MMA。
2. 在 **Microsoft Monitoring Agent**  >  **Azure Log Analytics (OMS)** 屬性中，確定工作區的 **狀態** 為綠色。
3. 如果狀態不是綠色，請嘗試移除工作區，然後再次將其新增至 MMA。

    ![MMA 狀態](./media/troubleshoot-assessment/mma-properties.png)

針對 Linux Vm，請確定 MMA 的安裝命令和相依性代理程式都已成功。 請參閱 [這裡](../azure-monitor/insights/service-map.md#post-installation-issues)的疑難排解指引。

## <a name="supported-operating-systems"></a>支援的作業系統

- **MMS 代理程式**：檢查支援的 [Windows](../azure-monitor/platform/agents-overview.md#supported-operating-systems)和 [Linux](../azure-monitor/platform/agents-overview.md#supported-operating-systems) 作業系統。
- **Dependency agent**：支援的 [Windows 和 Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) 作業系統。

## <a name="visualize-dependencies-for--hour"></a>將 > 小時的相依性視覺化

使用無代理程式相依性分析，您可以將相依性視覺化，或將其匯出到對應中最多30天的時間。

使用以代理程式為基礎的相依性分析，雖然 Azure Migrate 可讓您回到上個月的特定日期，但您可以將相依性視覺化的最大持續時間為一小時。 例如，您可以使用相依性對應中的 [持續時間] 功能來查看昨天的相依性，但您只能在一小時期間內查看這些相依性。 不過，您可以使用 Azure 監視器記錄來 [查詢](./how-to-create-group-machine-dependencies.md) 較長持續時間的相依性資料。

## <a name="visualized-dependencies-for--10-machines"></a>視覺化 > 10 部機器的相依性

在 Azure Migrate Server 評估版中，使用代理程式型相依性分析，您可以將具有最多10個 Vm [的群組](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) 相依性視覺化。 針對較大的群組，建議您將 Vm 分割成較小的群組，以將相依性視覺化。


## <a name="machines-show-install-agent"></a>機器會顯示「安裝代理程式」

將啟用相依性視覺效果的機器遷移至 Azure 之後，機器可能會顯示「安裝代理程式」動作，而不是「查看相依性」，因為下列行為：

- 遷移至 Azure 後，會關閉內部部署機器，且對等的 Vm 會在 Azure 中啟動。 這些機器會取得不同的 MAC 位址。
- 根據您是否保留內部部署 IP 位址，電腦可能也會有不同的 IP 位址。
- 如果 MAC 和 IP 位址與內部部署不同，Azure Migrate 不會將內部部署機器與任何服務對應相依性資料產生關聯。 在此情況下，它會顯示安裝代理程式的選項，而不是用來查看相依性。
- 測試遷移至 Azure 之後，內部部署機器仍會如預期般開啟。 在 Azure 中啟動的對等機器會取得不同的 MAC 位址，而且可能會取得不同的 IP 位址。 除非您封鎖來自這些電腦的傳出 Azure 監視器記錄流量，否則 Azure Migrate 不會將內部部署機器與任何服務對應相依性資料建立關聯，因此會顯示安裝代理程式的選項，而不是用來查看相依性。

## <a name="dependencies-export-csv-shows-unknown-process"></a>相依性匯出 CSV 顯示「未知的進程」
在無代理程式相依性分析中，會以最佳方式來捕獲進程名稱。 在某些情況下，雖然會捕捉來源和目的地伺服器名稱和目的地埠，但在相依性的兩端判斷進程名稱並不可行。 在這種情況下，進程會標示為「未知進程」。

## <a name="my-log-analytics-workspace-is-not-listed-when-trying-to-configure-the-workspace-in-server-assessment"></a>嘗試在伺服器評量中設定工作區時，未列出我的 Log Analytics 工作區
Azure Migrate 目前支援在「美國東部」、「東南亞」和「西歐」區域中建立 OMS 工作區。 如果工作區是在其他任何區域的 Azure Migrate 之外建立的，則目前無法與 Azure Migrate 專案相關聯。


## <a name="capture-network-traffic"></a>捕獲網路流量

收集網路流量記錄，如下所示：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按 F12 以啟動開發人員工具。 如有需要，請清除  **導覽設定上的清除專案** 。
3. 選取 [ **網路** ] 索引標籤，然後開始捕獲網路流量：
   - 在 Chrome 中，選取 [保留記錄]。 記錄應該會自動啟動。 紅色圓圈表示正在捕捉流量。 如果未顯示紅色圓圈，請選取黑色圓形以開始。
   - 在 Microsoft Edge 和 Internet Explorer 中，記錄應該會自動啟動。 如果沒有，請選取綠色的 [播放] 按鈕。
4. 嘗試重現錯誤。
5. 您在記錄時發生錯誤之後，請停止錄製，並儲存一份記錄的活動：
   - 在 Chrome 中，以滑鼠右鍵按一下並選取 [ **與內容一起另存為 HAR**]。 此動作會將記錄壓縮並匯出為 har 檔案。
   - 在 Microsoft Edge 或 Internet Explorer 中，選取 [ **匯出已捕捉的流量** ] 選項。 此動作會壓縮並匯出記錄。
6. 選取 [ **主控台** ] 索引標籤，以檢查是否有任何警告或錯誤。 若要儲存主控台記錄：
   - 在 Chrome 中，以滑鼠右鍵按一下主控台記錄的任何位置。 選取 [ **另存** 新檔]、匯出並壓縮記錄檔。
   - 在 Microsoft Edge 或 Internet Explorer 中，以滑鼠右鍵按一下錯誤，然後選取 [ **全部複製**]。
7. 關閉 Developer Tools。


## <a name="where-is-the-operating-system-data-in-my-assessment-discovered-from"></a>我的評量中的作業系統資料是從哪裡探索而來？

- 針對 VMware Vm，根據預設，它是 vCenter 所提供的作業系統資料。 
   - 針對 VMware linux Vm，如果已啟用應用程式探索，則會從來賓 VM 提取 OS 詳細資料。 若要檢查評量中的作業系統詳細資料，請移至 [探索到的伺服器]，並將滑鼠移至 [作業系統] 資料行中的值。 在快顯的文字中，您將能夠看到您看到的作業系統資料是從 vCenter server 收集，還是從使用 VM 認證的來賓 VM 收集而來。 
   - 若為 Windows Vm，系統一律會從 vCenter Server 提取作業系統詳細資料。
- 針對 Hyper-v Vm，系統會從 Hyper-v 主機收集作業系統資料
- 若為實體伺服器，則會從伺服器提取。

## <a name="next-steps"></a>後續步驟

[建立](how-to-create-assessment.md) 或 [自訂](how-to-modify-assessment.md) 評量。